# use-remote-jupyter-notebook
How to use notebooks on your remote server in your local browser via ssh.

### first possibility (not robust against connection interruptions)

1. Make sure that jupyter is installed on both the remote server and your local machine.

2. Open a terminal on your local machine, log into your remote host via ssh, go to the directory were your notebooks are and start jupyter without browser:
``` bash
ssh 'username'@'your-remote-server'
cd 'your-notebook-directory'
jupyter notebook --no-browser --port=8889
```
### Don't close this terminal! 
(Jupyter will output a url including a token that you will need later on.
Alternatively, you can set a password for remote access to jupyter latter on.)

3. Open another terminal on your local machine and type:
``` bash
ssh -N -f -L localhost:8888:localhost:8889 'username'@'your-remote-server'
```

4. Open a browser window and enter (in the adress field):
localhost:8888

4.a You will be prompted with a dialogue asking you to enter a token to proceed. You can find this token in the terminal output of jupyter in step 1. on your remote server. Alternatively, you can set a password for remote use of jupyter here, using the aforementioned token.

4.b Now you should see a jupyter browser menu with your notebook files. Choose one and start working.

### If your connection breaks:
It might happen that you will be confronted with some error like
``` bash
bind: Address already in use
channel_setup_fwd_listener_tcpip: cannot listen to port: 8888
Could not request local forwarding.
```
in step 3., when you try to start a remote jupyter session anew, following the steps above.
This issue can be resolved by finding and killing the zombie process still using port 8888:
``` bash
lsof -ti:8888 | xargs kill -9
```
Now everything should work fine again.

### second possibility (robust against connection interruptions)

1. Make sure that jupyter is installed on both the remote server and your local machine.
1.b Make sure tmux (teminal multiplexer) is installed on your remote.

2. Open a terminal on your local machine, log into your remote host via ssh, go to the directory were your notebooks are, open a tmux window inside the terminal and start jupyter without browser:
``` bash
ssh 'username'@'your-remote-server'
cd 'your-notebook-directory'
tmux
jupyter notebook --no-browser --port=8889
```
Now, if the ssh terminal is closed/terminated it will keep running your jupyter session on the remote instance.
(Jupyter will output a url including a token that you will need later on.
Alternatively, you can set a password for remote access to jupyter latter on.)

3. Open another terminal on your local machine and type:
``` bash
ssh -N -f -L localhost:8888:localhost:8889 'username'@'your-remote-server'
```

4. Open a browser window and enter (in the adress field):
localhost:8888

4.a You will be prompted with a dialogue asking you to enter a token to proceed. You can find this token in the terminal output of jupyter in step 1. on your remote server. Alternatively, you can set a password for remote use of jupyter here, using the aforementioned token.

4.b Now you should see a jupyter browser menu with your notebook files. Choose one and start working.

### If your connection breaks:

Just reconnect via
``` bash
ssh -N -f -L localhost:8888:localhost:8889 'username'@'your-remote-server'
```
and reload the notebook in your browser - it will still be running.

If you like to see the jupyter terminal session on your remote again (its still running in the background), just reconnect to your remote via ssh and type
``` bash
tmux attach
```
If you want to close the jupyter session, exit it as usual and terminate the tmux session via
``` bash
tmux detach
```
