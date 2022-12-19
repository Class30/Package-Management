@@ -15,8 +15,10 @@
+ Attach Security Group to EC2 Instance/nodes.

## Assign hostname &  login as ‘root’ user because the following set of commands need to be executed with ‘sudo’ permissions.
```sh
sudo hostnamectl set-hostname master
sudo -i
```

``` sh
# run the following below as a script
