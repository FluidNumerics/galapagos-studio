# Using SSH Key login on Galapagos

By default, you can log in to the Galapagos cluster over ssh using your [Cloud Identity Premium](https://cloud.google.com/identity) account username and password. 

!!! note
    Your username is the name prefixing the `@fluidnumerics.com` in your cloud identity account. For example, the username for `hello@fluidnumerics.com` is `hello`.

## Adding SSH Keys to your account
The Galapagos cluster supports using ssh key authentication. To use this feature, you need to add an SSH key to your Cloud Identity account. The easiest method for doing this is to use the [`gcloud` command line interface (CLI)](https://cloud.google.com/sdk/gcloud).

To get started, [**install the `gcloud` CLI**](https://cloud.google.com/sdk/docs/install).

Once installed, initialize `gcloud` on your system with

```
gcloud init
```

When prompted, be sure to log in with your provided `fluidnumerics.com` account.

After you have initialized the `gcloud` CLI with your `fluidnumerics.com` account, you can use the [`gcloud compute os-login ssh-keys add` command](https://cloud.google.com/sdk/gcloud/reference/compute/os-login/ssh-keys/add) to add a locally create ssh key to your cloud identity account.

E.g. To add the key in `$HOME/.ssh/id_rsa.pub` to your OS Login profile, run:

```
gcloud compute os-login ssh-keys add --key-file=$HOME/.ssh/id_rsa.pub
```