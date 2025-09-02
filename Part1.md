# Part 1: First Contact with the de.NBI Cloud

We will start slowly and get our very first virtual machine instance
running in the cloud.

The starting point for this tutorial is the de.NBI Cloud profile page
(<https://cloud.denbi.de/portal/>).

## 1.1 Create a de.NBI Cloud Account

If you do not have a de.NBI Cloud account, please register for one via
this link: <https://cloud.denbi.de/register>. You can read more about
the registration process in our de.NBI Cloud wiki:
<https://cloud.denbi.de/wiki/registration/>. Please make sure to to
click on "continue" if this button shows up.

If you successfully registered for a de.NBI Cloud account, you should be
able to log in to the de.NBI Cloud Portal:
<https://cloud.denbi.de/portal/>.

## 1.2 Set an SSH key in your account

1.  Click on the `Profile` tab on the left and scroll down .

2.  If you have no SSH key set so far, just click on generate key and
    save the private key. During this workshop you will not need this
    file because you will access all VMs via the browser. However, for
    your future work using SimpleVM, we highly recommend to read the
    de.NBI Cloud wiki regarding SSH keys:
    <https://cloud.denbi.de/wiki/portal/user_information/#ssh-key>

![Key](figures/key.png)

2.(optional) If you have already a personal ssh key-pair, you can also
upload your *public* key here instead of generating one.

## 1.3 Join the ib2025workshop1 Project

If you have not done yet, please click on the following link to request
access to the RDM Meets Cloud project:

ib2025workshop

## 1.4 Start a VM

1.  Under the `Virtual Machine` entry on the left menu, select
    `New Instance`.

2.  Select the (only available) project `rdm-meets-cloud` and choose a
    name for your VM (e.g. your login name) or a name will be selected
    automatically.

3.  Select **de.NBI default** as the flavor.

4.  In the image section, please select the `Snapshots` tab and select
    **RDM Meets Cloud Workshop** image. ![RDM
    Cloud](./figures/image.png) **de.NBI default** flavor. ![]
    (figures/deNBI_default_flav.png)

5.  Select the Research Environment tab and choose the following
    environment:

    -   Guacamole 2025 ![Guacamole](figures/Gucamole_2025.png)

6.  Confirm the checkboxes and click on Start.

Next to [Section 2](Part2.md)
