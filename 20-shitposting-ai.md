# The Shitposting AI - An Ironic Solution to Fruitless Online Debate

2022-05-13 10:00
TNG Technology Consulting - Innovation Hacking
Thomas Endres - Partner
Martin Fortsch - Principal Consultant
Jonas Mayer - Software Consultant

## Why Innovation Hacking?

Why give 10% of your time for employees on this?

- Learn something new
- Motivate our employees
- Just because

It also ends up grabbing the attention of the media which is good.

This can lead to a flood of "constructive" comments.

### Reaction

Typically, the best way to respond to these comments is to spam back at them.

Shitposter Shitpost -> TNG Giga Chad Reply -> Shitposter Shitpost -> TNG Giga Chad Reply -> etc. etc.

### Can we automate this?

They took an AI, the Jonas Simulator that used Jonas' voice trained on some of his blog posts and ramble on.

What happens if we take the Jonas Simulator and reply to the shitposter?

But this is a global problem of shit posting. On average people spend ~115 mins on social media a day. There's a huge waste or resources of people discussing online.

Let's take inspiration from the industrial revolution. People do repetetive tasks that then get automated with machines to make it easier.

So let's just have a machine that generates a shitpost, replies, generates a shitpost, replies etc. 

Now everyone can do something useful with their time!

We just need an AI that represents all of the opinions online!

### Robot Heads

To make this feel more human, let's use a robot head to provide all of these comments.

## What is this all about?

We need the brain to generate the comments and then a robot head to speak it out loud.

## NLP-Powered Robot Discussions.

NLP is the cross point between linguistics and computer science. It's used with

- Most of our virtual assistants.
- Automated summary generation.
- Sentiment analysis.

We created a mathematical model of neurons. We weight the inputs, provide a bias in the artificial neuton and a calculation function provides the final value.

So... how do we turn text into numbers so we can use this model?

Tokenisation: "Jonas Mayer is so so nice".

| 0   | 1   |2 |3 |3 | 4  |
|Jonas|Mayer|is|so|so|nice|

This turns the text into numbers. Each word is assigned a number.

We also have sub-word tokenizers.

With out tokened words, we put these into the input of our neural network.

They go through the first layer of the neural network, then the second layer and then produce a result at the output layer. If we have a spam detection algorithm, the might result in a 0.99 to show 99% likelihood of spam.

We then map this to a ground truth value of 0 or 1.

However, it's not super easy. All of the tokens have arbitrary numbers. Neural networks work on distance.

The numbers need to show how similar a word is to another. "Jonas" and "Human" should be closer than "so" and "Jonas" for example.

We need to do token embedding. We group things together by putting them closer or further from each other. Words that are closely related should have shorter distances, words that are less closely related should have longer distances between their token numbers.

## Generating Social Media Comments

OpenAI GPT - Seems to be perfect. It calls out specifically the automation of crappy content as a potential malicious use of it's tool on it's website.

GPT is a Generated Pretrained Transformer. It was already trained on the internet.

In the background GPT is doing next word prediction. "Jonas <pad> <pad> <pad> <pad>" is the input, then we get "Jonas Mayer <pad> <pad> <pad>". Then we feed that output back into the input until we find an end of text token. "Jonas Mayer is a dork <end>"

GPT has 12 layers. Which has a masked self-attention layer and a Feed Forward Neural Network.

### A masked self-attention layer

Like cheating. You look a little to the left, a little to the right and figure out what you're supposed to write on your paper.

GPT will look at the broader content of the sentence, it will look at the words previous to the one your interested in processing and then take that context and push it through the Feed Foward NN.

### Final Stack

                                         GPT
Input Tokens -> [ Token Embeddings -> Decoder Stack -> Token Embeddings ] -> Token Probabilities

GPT was trained on the general internet, so we need to train it on our custom shitposting dataset.

We take the input: "Your deep fake are shit <c> Screw ... is" and we have a predicted output "Your mom's fakes are nice <c> You ... good" and then train on those expectations.

## Collecting a Comment Dataset

Twitter and Reddit obviously.

We can use their APIs to look for information. They filtered down to some subreddits.

- /r/politics
- /r/changemyview
- /r/mechanicalkeyboards

You know the most controversial ones.

For twitter, there are API restrictions. You can only retrieve ~500,000 tweets per month, but that should be enough.

These datasets are often nested tree structures of replies. Then these threads need to be flattened to posts with the reply and and end of comment token in single chains of replies and comments.

### Continuous Training

They put comments into 14 buckets one for each day. The learning takes place on every bucket except the one currently being scraped.

Because of the two weeks context it will stay up to date with modern events.

## Creating Human Voices

Text to speech is a known problem. Google use Tacotron 2.

Input Characters -> Spectogram Prediction Network -> Mel Spectogram -> Vocoder -> Voice Waveform

We can have multiple voices with multiple trained instances of Tacotron.

But instead lets do a Tacotron that can do multiple voices.

We take a voice sample and encode the speaker.

But Tacotron isn't built for this. So they useds CoquiAI GlowTTS.

## Building the Robot Heads

TNG are pretty robot experienced. 

The hardware is a glass head with a Raspberry Pi inside. An arduino is attached to an LED ring at the front to show an animation and blink eyes. Then a downfiring audio speaker. Plus a 3D printed stand.

Several of these heads were created to have a discussion.

## Putting it all together

Take the brain and the robot heads together.

NLP takes a lot of compute and won't run on Pi. So you need to use a client/server architecture.

    Server
[GPT | TTS | Socket] -> send Wav, stop thinking and start talking -> Robot Head

This happens for each of the robot heads in order.

## Summary

We solved online debate, one of humanities most pressing issues.

What about speech to text? If we could interact with the heads? Mavbe that's next.
