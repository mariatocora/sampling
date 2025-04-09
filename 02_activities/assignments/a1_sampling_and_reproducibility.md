# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 100 (from the original 1000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: Maria C. Tocora

```
1. Stages in which sampling is occurring in the model: 
- First Sampling: Community with 1000 people (Simple size of 200 people attending the wedding and 800 people attending brunch) 
Function: events = ['wedding'] * 200 + ['brunch'] * 800 # Create DataFrame for people at events with initial infection and traced status.
- Second sampling: Subset community to infect people considering an ATTACK_RATE of 0.10. Sample size is 20 for weddings and 80 for brunches. Infected_indices is equal to 100, a value that corresponds to what is mentioned in the blog. 
Function:
  infected_indices = np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False) #Function np.random.choice() generates a random sample from a given 1-D array.
  ppl.loc[infected_indices, 'infected'] = True # Infect a random subset of people
- Third sampling: Randomly choose which infected people get traced considering a TRADE_SUCCESS 0f 0.20.
Function: 
  ppl.loc[ppl['infected'], 'traced'] = np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS #Function np.random.rand() creates an array of the given shape and populate it with random samples from a uniform distribution over [0, 1)
  ppl['traced'].value_counts()
  Outcome: # This outcome was obtained with a np.random.seed(123)
False    86
True     14
- Fourth sampling: Secondary contact tracing (i.e. tracing the contacts of contacts of known cases, to get ahead of the chain of transmission) based on event attendance by using the proportion of people infected during the primary contact tracing.
Function: 
  event_trace_counts = ppl[ppl['traced'] == True]['event'].value_counts()
  events_traced = event_trace_counts[event_trace_counts >= SECONDARY_TRACE_THRESHOLD].index
  ppl.loc[ppl['event'].isin(events_traced) & ppl['infected'], 'traced'] = True ()
At the end, we obtained a ppl['traced'].value_counts() equal to 100 -brunch_infections: 79 and wedding_infections: 21. This result varies from the results shown in the blog - 80 and 20 cases of infection reported for brunches and weddings, respectively.

2. The code does not produce the same graph posted in the blog, as the second and third sampling steps (see above) consider functions that generate random values which can change each simulation if a random seed is not set. 

3. The outcome graphs vary in frequency for each proportion of cases as the plotted values are produced after a random simulation that generates a different sequence of numbers each time we run the code. To guarantee reproducibility, we can set a random seed value as is shown below.

    4. New code: 
# Run the simulation 1000 times
np.random.seed(123)
results = [simulate_event(m) for m in range(100)]
props_df = pd.DataFrame(results, columns=["Infections", "Traces"])

# Plotting the results
plt.figure(figsize=(10, 6))
sns.histplot(props_df['Infections'], color="blue", alpha=0.75, binwidth=0.05, kde=False, label='Infections from Weddings')
sns.histplot(props_df['Traces'], color="red", alpha=0.75, binwidth=0.05, kde=False, label='Traced to Weddings')
plt.xlabel("Proportion of cases")
plt.ylabel("Frequency")
plt.title("Impact of Contact Tracing on Perceived Infection Sources")
plt.legend()
plt.tight_layout()
plt.show()

```

## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `23:59 - 09/04/2025`
* The branch name for your repo should be: `assignment-1`
* What to submit for this assignment:
    * This markdown file (a1_sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `assignment-1`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via the help channel in Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
