---
layout: post
title: "Making the Joy Division dataset for music mood classification public"
date: 2019-07-26
tags: [audio classification, machine learning, music, mfcc]
---

If you're looking at this, you're probably interested in a music dataset which has songs labeled by mood or valence.

I worked on a project called JoyDivision while I was a graduate student at George Mason University's Computer Science department. As such, while I'm interested in music information retrieval (MIR), I claim to have no knowledge of how datasets are created for machine learning experiments.

I'm finally making the dataset available publicly along with the methodology I used to create it, however, I make no guarantees as to whether this is a scientifically usable dataset for any serious research. I'd in fact advise you to read my methodology and use your own judgement as to whether this is an appropriate dataset for your work.

## Methodology

I use the Million Song Dataset's audio features (in the form of h5 files) and limit my experiment to a few thousand tracks from this dataset. Then, because MSD does not contain mood labels, I decide to hand label some songs. I've hand labeled a little over 7000 songs as 'happy' or 'sad'. To make the process of hand labeling these easier, I first looked up every artist and/or song I knew in the dataset and labeled the songs `happy` or `sad` manually. Later, I actually hacked together a bunch of scripts to make Spotify playlists out of the Million Song Dataset and started listening to some tracks randomly. For most songs, I'd listen to 30 seconds of the track at the minimum and then decide if the song seemed happy or sad. If I heard a song that made it hard to decide, I'd skip it and pick another track.

I tried doing this for as many tracks as possible. Luckily, because I listen to a lot of music, I could find several songs I already knew and didn't have to listen to them too carefully to be able to label them.

Once I had a sizeable chunk of labeled data and a roughly equal number of `happy` and `sad` tracks, I extracted features for them using the data from the Million Song Dataset and Spotify.

Spotify has a neat API for getting various features out of tracks — [https://developer.spotify.com/discover/](https://developer.spotify.com/discover/) — and I recommend you use this for any music related experimentation.

Now of course there are many biases in my methodology — I do not account for language or genrewise differences, in my experience of music, lyrics definitely influence how the song "feels" and I'm the only one who has labeled this dataset. Hence, I don't strongly advocate for this dataset except for exploratory and toy projects.

## Dataset

You can [download the data here](https://drive.google.com/drive/folders/1TIc3eQWKMuYB7fkIC5Q9MWTkFMBK2lV7?usp=sharing).

To get started with the data, all you need is two files, `fullset.pkl` and `fullset.csv`. The `fullset.pkl` file is a [numpy](https://numpy.org/) array stored as a pickled object. Of course, loading in random pkl files from the internet poses a great security risk. I don't know of another good way to easily distribute this file so if you have a suggestion, let me know (preferably one that doesn't involve me hosting a database).

You can now split this `fullset` into train and test datasets. The `Mood` column is the label.

However, you might want to know how the full set was created. Each song has a `File` in `fullset.csv` which refers to the h5 file name in the Million Song Dataset. To access the h5 files themselves and create features different from the ones I have in `fullset.pkl` you would have to read in the h5 files and work with them. The `fullset.pkl` file is a collection of features computed through different methods from the MSD or Spotify's audio features — think of it as a feature engineering artifact rather than a true raw dataset.

Here's the list of columns in `fullset.csv`. The `tim_` and `pitch_` features are calculated features for my methodology and not a part of the Million Song Dataset originally.

```
File, Artist, Title, TimeSignature, Key
SegmentsLoudMax, Mode, BeatsConfidence, Length, Tempo
Loudness, Timbre, Pitches, KeyConfidence
TrackID, Energy, Speechiness, Valence, Danceability
Acousticness, Instrumentalness, Mood, KeyMode, LoudnessSq
TempoMode, TimbreVector, PitchVector, TimbreAverage, TimbreAvgVariance, Beats
tim_0, tim_1, .... tim_89
timavg_0, timavg_1, .... timavg_11
pitch_0, pitch_1, .... pitch_11
```

Once you have all the titles of the songs you're using and labels for them, you can use the Spotipy API to query the track names and get track IDs for them along with Spotify's audio features. Join these 3 sources together and you have a labeled dataset with rich features.

If you only want some tracks from the Million Song dataset with the Track ID and my assigned label — look at `labels.csv`. You could use this to mine MSD for features and other track information.

I won't be providing all the intermediate files that get referenced in the [JoyDivision project](https://github.com/bhavika/JoyDivision) — I think this way is better.

You could get the Million Song Dataset here: [https://millionsongdataset.com/pages/getting-dataset/](https://millionsongdataset.com/pages/getting-dataset/).

## Is there another way?

Spotify's audio features have a valence score (between 0 to 1) that you could use for prediction tasks as your label.

There are MIREX tasks from previous years where you could find some data: [Audio Mood Classification](https://www.music-ir.org/mirex/wiki/2019:Audio_Classification_(Train/Test)_Tasks#Audio_Mood_Classification).

Hope this helps!
