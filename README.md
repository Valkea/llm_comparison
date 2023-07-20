# A simple comparison af various LLM models.

## The models

tiiuae/falcon-7b-instruct
mosaicml/mpt-7b-chat
meta-llama/Llama-2-7b-chat-hf
openai/gpt-3.5-turbo

## The infrastructure

We used *openai/gpt-3.5-turbo* as a reference model. But let's not forget that it isn't a 7B model and has been tested via the commercial API.

On the other side, *tiiuae/falcon-7b-instruct*, *mosaicml/mpt-7b-chat* and *meta-llama/Llama-2-7b-chat-hf* are all 7B models and have been tested on a Google Colab instance with a Tesla V100

## The prompts

tiiuae/falcon-7b-instruct :
> """ You are agent_2, a teenager. Answer agent_1 with an open-ended question and try to use the word 'orange'. Context: \n{context}agent_2:"""

mosaicml/mpt-7b-chat :
> """ You are agent_2, a teenager. Answer agent_1 with an open-ended question and try to use the word 'orange'. Context: \n{context}agent_2:"""

meta-llama/Llama-2-7b-chat-hf
> """ You are agent_2, a teenager. Answer agent_1 with an open-ended question and try to use the word 'orange'. No explanation, no code, no note. Context: \n{context}agent_2:"""

openai/gpt-3.5-turbo
> """ You are a teenager. Answer with an open-ended question and try to use the word 'orange'.""" followed by context.

## The test_set

To test the models, I extracted 50 dialogues from the [Topical-Chat](https://github.com/alexa/Topical-Chat/tree/master).
- all selected dialogues ended with a question
  - *(so that we can more accurately see if the model follows the unnatural request to answer with another question)*
- all selected dialogues were cut after 3 rounds
  - *(user/assistant/user)*

@inproceedings{Gopalakrishnan2019,
  author={Karthik Gopalakrishnan and Behnam Hedayatnia and Qinlang Chen and Anna Gottardi and Sanjeev Kwatra and Anu Venkatesh and Raefer Gabriel and Dilek Hakkani-Tür},
  title={{Topical-Chat: Towards Knowledge-Grounded Open-Domain Conversations}},
  year=2019,
  booktitle={Proc. Interspeech 2019},
  pages={1891--1895},
  doi={10.21437/Interspeech.2019-3079},
  url={http://dx.doi.org/10.21437/Interspeech.2019-3079}
}

## The (simple) metrics
- *mean_inference_time*: the average time needed for returning an answer
- *mean_response_size*: the average length of the answers
- *has_question*: the total number of answers containing a question mark (This was required in the provided context)
- *has_orange*: the total number of answers containing the word 'orange' (This was optional in the provided context)

## The results

model | mean_inference_time | mean_response_size | has_question | has_question_% | has_orange | has_orange_%
--- | --- | --- | --- | --- | --- | ---
mosaicml/mpt-7b-chat | 6.272755 | 115.62 | 26| 52.0% | 3 | 6.0%
tiiuae/falcon-7b-instruct | 9.881873 | 100.22 | 26 | 52.0% | 4 | 8.0%
meta-llama/Llama-2-7b-chat-hf | 2.369238 | 130.74 | 34 | 68.0% | 36 | 72.0%
openai/gpt-3.5-turbo | 1.636220 | 142.56 | 50 | 100.0% | 8 | 16.0%

> **Llama2** seems to be very promising according to these metrics:
> - it has good inference time (2.36s) considering the provided infrastructure,
> - it has successfully included open questions in most of the answers (68%),
> - it has successfully included the 'orange' word in most of the answers (72%) but the sentence may seem weird,
> - the sentences seem to consider the provided Context (human review),
> - the sentences seem to be grammatically correct (human review).
>
> Llama2 is commercially available in several versions (7B, 13B, 70B, instruct, chat ...) and it would be interesting to try more advanced models.

**But don't let those metrics fool us, they are simple demo metrics** which aren't sufficient to really evaluate the capacity of such models. The other 7B models have lower "has_question" and "has_orange" scores, but Llama2 answers with the 'orange' word often seem unnatural, so maybe the model misuses the 'orange' word (and we need other metrics to determine this).
