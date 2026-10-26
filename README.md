# Can a Small AI Model Understand Amharic Proverbs?

## Why I picked this

In Ethiopia proverbs are part of everyday talk. People use them to give advice, to make a point without saying it directly and to calm down an argument. If you only know the words you often miss the meaning, because the meaning comes from the culture.

I have been collecting Amharic proverbs and writing out their meanings for a dataset project. That made me curious. AI models are tested a lot on translation and general knowledge, but I rarely see tests that check whether a model understands this kind of cultural language. So I wanted to see what a small open model does with proverbs that almost every Ethiopian knows.

## The model

I used [Qwen3-4B](https://huggingface.co/Qwen/Qwen3-4B) on a free Google Colab T4 GPU. I picked it because it is recent and small enough to run for free. The Qwen3 family also says it supports more than 100 languages, so I expected it to know at least some Amharic. I turned off its "thinking" mode and set it to give the same answer every time so the results can be repeated.

## What I tested

I made a list of 20 well-known Amharic proverbs. For each one I wrote a word-for-word English translation and the real meaning. I also added 5 fake proverbs that sound like real ones but don't exist.

I asked the model about every proverb twice.

1. In Amharic script
2. As the word-for-word English translation

These were the two prompts.

```
What does this Amharic proverb mean? Answer in 2-3 sentences. {proverb in Amharic}

This is an Ethiopian proverb translated word for word into English: "{translation}". What does it mean? Answer in 2-3 sentences.
```

Asking both ways shows where the problem is. If the model fails in Amharic but does fine in English, it can't read Amharic well. If it fails in both, it doesn't understand the culture.

I scored each answer as correct, partly, wrong or made up. For the fake proverbs the right answer is to say it doesn't recognize them.

## Results

| | From Amharic | From English |
|---|---|---|
| Correct (out of 20 real proverbs) | 1 | 11 |
| Partly correct | 1 | 5 |
| Wrong | 18 | 4 |
| Fake proverbs it explained as real (out of 5) | 5 | 5 |

### 1. It can't really read Amharic

From Amharic it got only 1 out of 20 right. Even that one only worked because it swapped የጠመጠመ ሁሉ ቄስ አይደለም ("Not everyone who wears a turban is a priest") for the English saying "All that glitters is not gold." For most proverbs it made up a translation that has nothing to do with the words. For example it said ከድጡ ወደ ማጡ ("From the mud into the swamp") means "from a small step to a great leap."

### 2. It falls back on the same answers

When it didn't understand, it reused stock answers. Some version of "the one who is not careful will be caught in a trap" came up 4 times. "The first step is the hardest" came up twice. It also brought a wolf into 3 answers even though none of the proverbs mention a wolf.

### 3. English helps but the culture is still missing

In English it did much better but it still missed or half-missed 9 out of 20. Some mistakes were about everyday Ethiopian things. It said a jebena is a type of bread when it is the clay coffee pot. It said gesho is a stick when it is the plant used to brew tella and tej. It said a birile is a small clay cup when it is the glass flask for tej. It also read ዘመድና እሳት በሩቅ ("Relatives and fire, from a distance") as relatives being warm even from far away, which is the opposite of the real meaning.

### 4. It never says "I don't know"

It explained all 5 fake proverbs as if they were real, in both Amharic and English. For a fake proverb about injera it even gave the "translation" "A woman is like a camel that travels with a donkey."

### 5. Amharic gets broken into small pieces

The same proverb took 19 tokens in Amharic and 11 in English. The Amharic version has only 4 words so that is almost 5 tokens per word compared with about 1 in English. This is a sign the model saw very little Amharic during training. It also showed in the answers. Once it broke the letter ጀ into �� and once it copied ሆድ as ባድ.

## Why this matters

As AI tools reach more people in Ethiopia, more people will ask them about Amharic text. A model that confidently makes up meanings is risky, because people who can't check the answer might trust it.

## What could fix it

These are my ideas and I haven't tested them.

- Train on more Amharic text, especially everyday and cultural writing like proverbs, stories and conversations.
- Fine-tune on proverb and meaning pairs collected from native speakers, like the AfriProverbs dataset I contribute to.
- Use a tokenizer that handles the Ge'ez script better, so Amharic words aren't cut into so many pieces.
- Add unknown or fake sayings to the training data with answers like "I'm not sure this is a real proverb," so the model learns to admit when it doesn't know.

## Limitations

This is a small test. I used 25 proverbs, one model and one way of asking. I also scored the answers myself. Another native speaker might score a few of them differently.

## Files

- `amharic_proverbs.csv` has the proverbs, translations and real meanings.
- `results_scored.csv` has the model's answers, my scores and my notes.
- `Blind_Spots_of_Frontier_Models.ipynb` is the Colab notebook I used.

## How to run it

Open the notebook in Google Colab and choose a T4 GPU under Runtime > Change runtime type. Upload `amharic_proverbs.csv` and run the cells in order.