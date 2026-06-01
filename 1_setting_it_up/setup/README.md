## Install OpenClaw

### macOS / Linux

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### Windows

**WSL2 is recommended.** Open PowerShell as Administrator and run:

```powershell
wsl --install
```

Restart when prompted, then open the Ubuntu terminal and use the curl command above.

If you want to skip WSL2 and try a native Windows install:

```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

Note: plugin installs can fail on native Windows with a `spawn npm ENOENT` error. WSL2 fixes this.

### Verify the install

```bash
openclaw --version
```

If you get "command not found", close and reopen your terminal - the PATH update needs a fresh shell.

---

## Run the onboarding wizard

1. Run the command

![](<setup.png>)

2. Choose Quickstart

![](<setup 2.png>)

3. Go to more

![](<setup 3.png>)

4. Choose custom provider

![](<setup 4.png>)

5. Add the Tensorix API base URL: `https://api.tensorix.ai/v1`

![](<setup 5.png>)

6. Get the api from your account and paste it in here.

![](<setup 6.png>)

7. Choose OpenAI compatible

![](<setup 7.png>)

8. Type in the model you want to use. 
We recommend: 
- `z-ai/glm-5.1`
- `deepseek/deepseek-v4-pro`
- `moonshotai/kimi-k2.6`

![](<setup 8.png>)

9. Choose yes / no depending on the model (they usually do not)
In this case glm does not.

![](<setup 9.png>)

10. Choose telegram

![](<setup 10.png>)

	10.1. Open telegram app, search for `BotFather` and follow the steps.
![](<telegram1.png>)
![](<telegram2.png>)
	10.2. When it asks you for an username it needs to be unique in the Telegram world. So you might need a long one.
![](<telegram3.png>)
	10.3 Done
![](<telegram4.png>)

11. Copy the code and paste it back

![](<setup 11.png>)

12. Choose a search provider.
Both Brave and exa are good. You'll need to open a free account with them and get an API key

![](<setup 12.png>)

13. Choose a unique name for your bot

![](<setup 13.png>)

14. Skip setting up skills

![](<setup 14.png>)

15. Skip the rest of these options, we'll set them up later if we need them

![](<setup 15.png>)

16. Choose Hatch in terminal

![](<setup 16.png>)

17. Send it a message by setting a name and an overall reason for its existence: `Your name is Clue, you are a helpful assistant.`

![](<setup 17.png>)

18. Finished

![](<setup 20.png>)
