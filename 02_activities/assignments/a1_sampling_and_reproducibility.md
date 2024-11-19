# ASSIGNMENT: Sampling and Reproducibility in Python

Read the blog post [Contact tracing can give a biased sample of COVID-19 cases](https://andrewwhitby.com/2020/11/24/contact-tracing-biased/) by Andrew Whitby to understand the context and motivation behind the simulation model we will be examining.

Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

Run the Python script file called whitby_covid_tracing.py as is and compare the results to the graphs in the original blog post. Does this code appear to reproduce the graphs from the original blog post?

Modify the number of repetitions in the simulation to 1000 (from the original 50000). Run the script multiple times and observe the outputted graphs. Comment on the reproducibility of the results.

Alter the code so that it is reproducible. Describe the changes you made to the code and how they affected the reproducibility of the script file. The output does not need to match Whitby’s original blogpost/graphs, it just needs to produce the same output when run multiple times

# Author: YOUR NAME

```
# Author: LY NGUYEN

# Question 1: 
Examine the code in `whitby_covid_tracing.py`. Identify all stages at which sampling is occurring in the model. Describe in words the sampling procedure, referencing the functions used, sample size, sampling frame, any underlying distributions involved, and how these relate to the procedure outlined in the blog post.

# Answer: 

Refering to the 'simulate_event' function in the python notebook includes multiple stages where sampling occurs. 


#### 1. Infecting a Subset of Individuals
We pick some people to "infect" in our study. 

    Code: 
        '''
        # Infect a random subset of people

        infected_indices = np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False)

        ppl.loc[infected_indices, 'infected'] = True
        
        '''

- *The Sampling Function used:* **np.random.choice(ppl.index, size=int(len(ppl) * ATTACK_RATE), replace=False)**
- *The Sampling Frame:* All people who attended weddings or brunches (ie. 200 wedding attendants; and 800 brunch attendants) - ('ppl.index') - which is 1.000 people in our example. 
- *The Sample Size:* **100 people** (ie. **ATTACK_RATE = 10%** of the 1.000 people) are randomly chosen to be infected with Covid. 
- *The Sampling Procedure:* The function 'np.random.choice' combined with 'replace=False' means it will randomly select 100 people to infect, without picking the same person twice. 
- *The underlying Distribution:* **Uniform distribution** (each individual has an equal probability of being chosen). 
- *Relation to the Blog Post:* This code tries to simulate how an infection might spread in the real world, by 'flipping a coin' to decide who gets infected. But, since it's random, it may pick too many or too few from certain groups (e.g., 50 people at weddings, and 50 people at brunches), potentially leading to biased results (e.g., wedding attendants may get picked more, and therefore may get traced more). 


#### 2. Primary Contact Tracing
We try to find out which infected people we can trace.

    Code: 
        '''
        # Primary contact tracing: randomly decide which infected people get traced
        ppl.loc[ppl['infected'], 'traced'] = np.random.rand(sum(ppl['infected'])) < TRACE_SUCCESS
        
        '''


- *The Sampling Frame:* All infected people ('ppl.loc[infected_indices, 'infected'] = True) - which is 100 people in our example.
- *The Sample Size:* 100 people ('sum(ppl['infected']). 
- *The Sampling Procedure:* For each of the 100 infected people, we generate a random number between 0 and 1 (ie. between 0% - 100% chance of an infection being traced to a source event). We're trying to simulate the real world where imperfect tracing happens, meaning only 20% of infected people get traced. 
- *The underlying Distribution:* **Uniform distribution**, because the random numbers (between 0 and 1) are randomly generated for each infected person. 
- *Relation to the Blog Post:* Whitby discusses how this 'primary contact tracing' may lead to biased results in reality, because, in simple terms: it's just 'rolling a dice' to decide a small fraction (20% of infected people) to trace. Because it's random, it may pick too many or too few from certain groups (e.g., 10 infected individuals at weddings, 10 infected individuals at brunches), potentially leading to biased results.

#### 3. Secondary Contact Tracing
We see which events have enough traced people and then trace everyone at those events.

    Code: 
        '''
        # Secondary contact tracing based on event attendance
        event_trace_counts = ppl[ppl['traced'] == True]['event'].value_counts()
        events_traced = event_trace_counts[event_trace_counts >= SECONDARY_TRACE_THRESHOLD].index
        ppl.loc[ppl['event'].isin(events_traced) & ppl['infected'], 'traced'] = True
        '''

- *The Sampling Frame:* The events (weddings or brunches) with >2 traced individuals ('SECONDARY_TRACE_THRESHOLD = 2'). 
- *The Sample Size:* If an event (like a wedding or brunch) has at least 2 traced people, then we trace all infected people at that event.
- *The Sampling Procedure:* If we find >2 traced people at an event, we'll trace everyone infected there.
- *The underlying Distribution:* This is a deterministic procedure rather than a probabilistic one, as it depends entirely on whether a threshold (ie. 2 traced people) is met.  
- *Relation to the Blog Post:* Whitby discusses how clusters of individuals attending the same event are more likely to be traced, potentially leading to more biases.  


### In Summary: 
- Whitby's Blog talks about how reality of imperfect tracing (due to faulty patient recall and shortage of staff) as well as these tracing rules can create biases, because not everyone gets traced equally, and certain groups might be overrepresented (e.g., big events like weddings). 



```


## Criteria

|Criteria|Complete|Incomplete|
|--------|----|----|
|Altercation of the code|The code changes made, made it reproducible.|The code is still not reproducible.|
|Description of changes|The author explained the reasonings for the changes made well.|The author did not explain the reasonings for the changes made well.|

## Submission Information

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

### Submission Parameters:
* Submission Due Date: `HH:MM AM/PM - DD/MM/YYYY`
* The branch name for your repo should be: `sampling-and-reproducibility`
* What to submit for this assignment:
    * This markdown file (sampling_and_reproducibility.md) should be populated.
    * The `whitby_covid_tracing.py` should be changed.
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sampling/pull/<pr_id>`
    * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:
- [ ] Create a branch called `sampling-and-reproducibility`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack at `#cohort-3-help`. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.
