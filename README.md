# Accessing Video Transcripts via Vimeo API

[![DOI](https://zenodo.org/badge/1022289892.svg)](https://doi.org/10.5281/zenodo.16113457)

The `include_transcript=true` parameter provides comprehensive details, enabling the identification of auto-generated or specific transcript tracks.

<img width="1695" height="722" alt="image" src="https://github.com/user-attachments/assets/ff67dc96-ff8f-4e2f-a8d7-7d4e18b169bb" />


### Request: Listing Text Tracks

To begin, make a `GET` request to the `/videos/{VIDEO_ID}/texttracks` endpoint, ensuring to include the `include_transcript=true` parameter. This will return a list of all associated text tracks, from which the desired transcript track can be identified.

```bash
curl -X GET \
  -H "Authorization: Bearer {YOUR_ACCESS_TOKEN}" \
  -H "Accept: application/vnd.vimeo.*+json;version=3.4" \
  "[https://api.vimeo.com/videos/](https://api.vimeo.com/videos/){VIDEO_ID}/texttracks?include_transcript=true"
```
### Example Response (Excerpt):

Upon successful execution, a `JSON` response detailing available text tracks will be returned. This example highlights auto-generated subtitle tracks in various languages, including English, which would serve as the transcript.

```JSON
{
  "total": 5,
  "data": [
    {
      "uri": "/videos/{VIDEO_ID}/texttracks/{TEXTTRACK_ID_BG}",
      "active": true,
      "type": "subtitles",
      "language": "bg-x-autogen",
      "display_language": "Bulgarian (auto-generated)",
      "id": "{TEXTTRACK_ID_BG}",
      "link": "[https://captions.cloud.vimeo.com/captions/](https://captions.cloud.vimeo.com/captions/){TEXTTRACK_ID_BG}.vtt?expires={EXPIRY}&sig={SIG}&download=Bulgarian+%28AI-generated%29.vtt",
      "name": "Bulgarian (AI-generated)",
      "provenance": "autogen_dubbed"
    },
    {
      "uri": "/videos/{VIDEO_ID}/texttracks/{TEXTTRACK_ID_ES}",
      "active": true,
      "type": "subtitles",
      "language": "es-x-autogen",
      "display_language": "Spanish (auto-generated)",
      "id": "{TEXTTRACK_ID_ES}",
      "link": "[https://captions.cloud.vimeo.com/captions/](https://captions.cloud.vimeo.com/captions/){TEXTTRACK_ID_ES}.vtt?expires={EXPIRY}&sig={SIG}&download=Spanish+%28AI-generated%29.vtt",
      "name": "Spanish (AI-generated)",
      "provenance": "autogen_dubbed"
    },
    {
      "uri": "/videos/{VIDEO_ID}/texttracks/{TEXTTRACK_ID_EN}",
      "active": true,
      "type": "subtitles",
      "language": "en-x-autogen",
      "display_language": "English (auto-generated)",
      "id": "{TEXTTRACK_ID_EN}",
      "link": "[https://captions.cloud.vimeo.com/captions/](https://captions.cloud.vimeo.com/captions/){TEXTTRACK_ID_EN}.vtt?expires={EXPIRY}&sig={SIG}&download=auto_generated_captions.vtt",
      "name": "auto_generated_captions.vtt",
      "provenance": "autogen_source_audio"
    }
    # ... additional languages omitted for brevity
  ]
}
```
Upon receipt of the response, developers can identify the id of the desired text track based on language, display_language, or provenance (e.g., `autogen_source_audio` for the original auto-generated transcript). Subsequently, a `GET` request is made to the dedicated `/transcripts` endpoint, using the identified `TEXTTRACK_ID`, to retrieve the content. This content is typically in WebVTT format, requiring parsing to extract the plain text for integration.

###  Request for Transcript Content
To retrieve the raw transcript content, use the `TEXTTRACK_ID` obtained from the previous step in a `GET` request to the `/transcripts` endpoint.

```bash
curl -X GET \
  -H "Authorization: Bearer {YOUR_ACCESS_TOKEN}" \
  -H "Accept: application/vnd.vimeo.*+json;version=3.4" \
  "[https://api.vimeo.com/videos/](https://api.vimeo.com/videos/){VIDEO_ID}/transcripts/{TEXTTRACK_ID_EN}"
```

### Example Response (Excerpt):
```JSON
{
    "total": 71,
    "data": [
        {
            "id": "072f5965-d9b3-4047-86c8-d6a2021388cd#242248428#79",
            "cue_start": 79,
            "cue_end": 4370,
            "lines": [
                {
                    "text": "El siglo veintiuno ha transformado",
                    "voice": null
                },
                {
                    "text": "fundamentalmente la práctica",
                    "voice": null
                }
            ],
            "language": "es-x-autogen"
        },
        {
            "id": "072f5965-d9b3-4047-86c8-d6a2021388cd#242248428#4370",
            "cue_start": 4370,
            "cue_end": 9424,
            "lines": [
                {
                    "text": "archivística. Los archivistas ya no son",
                    "voice": null
                },
                {
                    "text": "únicamente custodios de artefactos",
                    "voice": null
                }
            ],
            "language": "es-x-autogen"
        },
        {
            "id": "072f5965-d9b3-4047-86c8-d6a2021388cd#242248428#9424",
            "cue_start": 9424,
            "cue_end": 14339,
            "lines": [
                {
                    "text": "tangibles, sino que deben navegar un",
                    "voice": null
                },
                {
                    "text": "ecosistema de información complejo,",
                    "voice": null
                }
            ],
            "language": "es-x-autogen"
        }
            # ... additional lines omitted for brevity
    ]
}

```

