# TrackSubtract
Automatic removal of copyrighted music from audio streams.

2018.06.06 MPKT

## Goal
Most major video platforms scan content for copyrighted audio. When infringing audio is detected in the background of a file, it is either deleted or muted. Either option results in fewer files, less revenue, and frustrated users. TrackSubtract provides a one-click product for removing unwanted copyrighted music from any audio file.

## Algorithm
A user submits an audiofile labeled `SongAndSignal` that contains both the desired audio and a undesirable background song.
-  A music identification service (such as the open-source EchoPrint) identifies the song track in the background
-  A file containing labeled `SongTrue` is obtained, containing a clean version.
-  Signal correlation between the `SongAndSignal` and `SongTrue` is calculated as a function of lag time to identify the temporal offset.
-  A sliding correction window (in the time domain) will scan over the song to match the amplitude of `SongTrue` to `SongAndSignal` before subtraction, since this will vary over the course of the recording. Thus the pre-subtraction signal attenuation factor (A*) is empirically determined as a function of time.
-  In real situations, the attenuation will not be consistent across all frequencies (trivial example: music that was accidentally recorded while being played from a phone speaker will not contain the bass frequencies that are present in `SongTrue`). Consequently, it may be helpful to pass a sliding frequency window within the sliding time window. In this case, the pre-subtraction signal attenuation (A*) is a calculated as a function of time and frequency.
-  The `SongTrue` is waveform is inverted, scaled by A*, and added to `SongAndSignal`.

## Data product:
### Consumer/individual interface
Users arrive at a webpage with 4 feature:
-  Select file to upload
-  Enter payment information (if new/unregistered user)
-  Select output format
-  Press "GO" to begin

### High-volume customer API
High-volume users (e.g. platforms for video sharing, etc) can access a pay-per-second API for removing music from audio tracks en masse

## Data
The `SongTrue` file contains a recording of the song "Hey" from the BenSound.com royalty-free audio website.

The `SongAndSignal` file contains a recording of me talking, while "Hey" plays in the background.

## Preliminary results:
The below figures each show a visual representation the two files described above:
-  **Top:** The recording of my voice while Hey plays in the background (`SongAndSignal`).
-  **Bottom:** The pure recording of the song (`SongTrue`)

`TracksWaveform_marked.png` shows both files represented as waveform time series:
![TracksWaveform](TracksWaveform_marked.png)

`TracksSectral_marked.png` shows the spectral representation:
![TracksSpectral](TracksSpectral_marked.png)

## Misc Notes
-  Looking through the spectral lens, the task at hand can be viewed as subtraction of surfaces in 2-dimensional {time, frequency} space. From this perspective, the A* attenuation factor can be thought of as the scaling factor for mapping between the surfaces, empirically based on each point's neighborhood (whose size is defined by the width of the time & frequency windows).

-  As a separate approach from the method described above, it might be easier to simply apply blind source separation methods (perhaps singular spectrum analysis / SSA) to `SongAndSignal`, then only recombine sources that don't show strong signal correlation with `SongTrue`.
