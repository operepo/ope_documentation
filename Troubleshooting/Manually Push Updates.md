
(Go to server sync binaries folder -> bin - Click git-cmd.exe to open a git command prompt)

(Do "dir" command - should show you in the same folder - git-cmd.exe should be present)

(Change folder to the proper repo)
cd ../volumes/repos/ope_laptop_binaries.git

(Check the list of remotes)
git remote -v

(You are looking for ope_laptop_binaries_ope_offline - if it points to the IP of the SMC server, skip to PUSH)
ope_laptop_binaries_ope_offline ssh://root@192.168.10.26:/ope/volumes/smc/git/ope_laptop_binaries.git (fetch)

(To remove an incorrect remote)
git remote remove ope_laptop_binaries_ope_offline

(To re-add it with correct info -- UPDATE IP ADDRESS FOR YOUR OPE SERVER)
git remote add ope_laptop_binaries_ope_offline ssh://root@192.168.10.26:/ope/volumes/smc/git/ope_laptop_binaries.git

(PUSH)
(Send the master branch to the remote smc/ope server)
git push ope_laptop_binaries_ope_offline master

