# Save Mastodon Bookmarks to Wallabag Automatically

Every 5 minutes, this workflow saves your new Mastodon bookmarks to Wallabag, skipping the URLs that are already there.

## How it works

1. A schedule trigger starts the workflow every 5 minutes.
2. The last processed bookmark ID (`min_id`) is read from a **Data Table**, so only new bookmarks are fetched.
3. New bookmarks are fetched from the Mastodon API, and the pagination header is parsed to get the next `min_id`.
4. Each bookmark is checked against Wallabag (**Check exists**) before being saved.
5. The URL saved depends on the toot:
   - the **link card** URL if the toot has one,
   - otherwise the **first link** of the toot (hashtags and mentions excluded),
   - otherwise the **toot itself**.
6. The new `min_id` is stored in the Data Table for the next run.

## Requirements

- n8n with **Data Tables**
- The community node [`n8n-nodes-wallabag`](https://www.npmjs.com/package/n8n-nodes-wallabag)
- A Mastodon access token with the `read:bookmarks` scope
- A Wallabag API client (client ID and secret)

## Setup

1. Install the community node `n8n-nodes-wallabag`.
2. Create a Mastodon access token with the `read:bookmarks` scope.
3. Add an **HTTP Bearer Auth** credential with this token and select it in *Fetch new bookmarks*.
4. Replace `YOUR_MASTODON_INSTANCE` in the *Fetch new bookmarks* URL.
5. Create a **Wallabag API** credential and select it in the 4 Wallabag nodes.
6. Create a Data Table `mastodon_sync_state` with two string columns, `source` and `min_id`, and select it in *Get sync state* and *Save min_id*.
7. (Optional) Set `DEFAULT_MIN_ID` in *Prepare min_id*. With `0`, the first run starts from your oldest bookmarks.
8. Test with *Execute workflow*, then activate it.

## Notes

- The first run with `DEFAULT_MIN_ID = 0` can process many bookmarks at once. Test with a recent ID if your list is long.
- Mastodon returns at most 20 bookmarks per call by default, so a large backlog is processed over several runs.
