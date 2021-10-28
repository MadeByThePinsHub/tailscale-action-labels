# Tailscale GitHub Action with ACL Labels Support

An manual-written fork of <https://github.com/tailscale/github-action>, with support for ACL tags. Currently only available on Linux, but PRs are generally welcome. :)

## Usage

```yml
    - name: Setup Wireguard VPN through Tailscale
      uses: MadeByThePinsHub/tailscale-action-labels
      with:
        # secret name may varies on your org
        authkey: ${{ secrets.TAILSCALE_RECAPTIMEBOT_CICD_KEY }}
        labels: tag:gh-actions,tag:linux,tag:msft-azure,tag:ssh
        # Going to unstable is risky, beware!
        version: 1.17.91
```

### Supported Configuration

* `authkey`: The Tailscale pre-auth key, usually an ephemeral authkey, to ensure your tailnet's machines' list don't clutter.
* `labels`: ACL labels for your tailnet admins' rules, defaults to our configuration `tag:gh-actions,tag:linux,tag:msft-azure`, where:
  * `tag:gh-actions` is an GitHub Actions runner, either hosted by M$FT/GitHub or self-hosted.
  * `tag:linux` is our meta-label for Linux machines, not required to add per-distro labels.
  * `tag:msft-axure` is our another meta-labels for VMs managed on Azure.
* `version`: Use this to install Tailscale from unstable track (tl;dr the minor version should be divisible by 2), check <https://pkgs.tailscale.com/unstable/> for latest unstable version.

## Troubleeshooting and Notes

* Remember NOT to enable manual approval on newer devices, since this will make execution time longer + may hit the timeout limit of 30 days in GitHub if left alone.
* The default labels assumes they have atleast one tagOwner on your tailnet's ACL policy. If not, they would consider as invalid tags so add these JSON code from our ACL config below. Make sure to customize these before hitting Save!

```json
  "tagOwners": {
    "tag:linux": [
      "group:admins",
      "group:squad",
      "group:bots"
    ]
    "tag:gh-actions": [
      "group:bots"
    ],
    "tag:msft-azure": [
      "group:admins",
      "group:squad",
      "group:bots"
    ]
  }
```
