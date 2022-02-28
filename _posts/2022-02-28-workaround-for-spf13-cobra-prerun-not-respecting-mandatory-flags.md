---
layout: post
title: "spf13/cobra not respecting mandatory flags as part of Prerun"
description: "spf13/cobra not respecting mandatory flags as part of Prerun"
tags: ["golang"]
comments: true
share: true
cover_image: '/content/images/2022/02/cobra-golang.png'
---


Just a continuation of the tweet, adding into small snippets for context.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Came across this issue where spf13/cobra would not work for mandatory flags set, when PreRun is set for a command while building a cli tool <a href="https://t.co/GmULRFrFfm">https://t.co/GmULRFrFfm</a> (1/n)</p>&mdash; Tasdik Rahman (@tasdikrahman) <a href="https://twitter.com/tasdikrahman/status/1498355645392818178?ref_src=twsrc%5Etfw">February 28, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Context

During the `Run` directive which you have present

```golang
// sample snippet from https://cobra.dev/#prerun-and-postrun-hooks
...
...
func main() {

  var rootCmd = &cobra.Command{
    Use:   "root [sub]",
    Short: "My root command",
    PreRun: func(cmd *cobra.Command, args []string) {
      // Logic for PreRun
    },
    Run: func(cmd *cobra.Command, args []string) {
      // Logic for Run
    },
...
...
```

Now in case you have added mandatory flags inside your `init()` function

```golang
...

func init() {
  ...
  // picked from  https://cobra.dev/#required-flags
  rootCmd.Flags().StringVarP(&Region, "region", "r", "", "AWS region (required)")
  rootCmd.MarkFlagRequired("region")
  ...
}
...
```

Now if you run the cli without passing the required flag or `r` when running the cli, the routines inside `PreRun` don't get run, and this flag is not respected anymore.

## Workaround

Just move the flow inside the `PreRun` to `Run`, as there is no workaround as of now, even the same has been done by some other projects like [keptn](https://github.com/keptn/keptn/issues/2729), where they have done the same. Agreed this bloats the `Run`, directive, but until there is a better solution, it's better than not having any validation and repeating what the framework has already done for you.

I didn't find anything, the last open ticket on [this](https://github.com/spf13/cobra/issues/655) is still open, I ended up [enquiring](https://github.com/spf13/cobra/issues/655#issuecomment-1054509187) in case this is by design, in case I have missed something. Will check back and update here in case there is an update.

There was an old [issue](https://github.com/spf13/cobra/issues/206) which talks about the introduction of the mandatory flags, but doesn't go into the `PreRun` directives.

## References

- https://github.com/keptn/keptn/issues/2729
- https://github.com/spf13/cobra/issues/655
