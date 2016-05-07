(copy of https://github.com/solvedio/checklists/blob/master/signing-node-webkit-app-for-mac.md )


#Signing a Node-Webkit Mac Application
(Tested on 10.9.5)

## A. Request Certificate

Admin or Member:

- [ ] Open "Keychain Access" on your Mac
- [ ] In the menu bar: "Keychain Access" > "Certificate Assistant" > "Request a Certificate From a Certificate Authority"
- [ ] Ensure "User Email Address" and "Common Name" are present, select "Save to disk" and "Continue".
- [ ] Send the newly created file to your agent


## B. Generate Certificate

Agent (Admin are not allowed to create "Developer ID" certificates - http://stackoverflow.com/a/21695283):

- [ ] Go to https://developer.apple.com/account
- [ ] Sign up and go to "Mac Apps" > "Certificates" > "+"
- [ ] Select "Developer ID" at the bottom of the list and "Continue"
- [ ] Select "Developer ID Application" and "Continue"
- [ ] Select "Continue"
- [ ] Upload the CSR file sent by admin (or dev)
- [ ] Download the certificate
- [ ] Send it to the requester

## C. Install Certificate

Admin or Member:

- [ ] Add the certificate in your keychain by double-clicking it
- [ ] Copy "User ID" in clipboard

## D. Sign App

Admin or Member:

- [ ] Go to Terminal in the folder containing your .app
```
$ app="Your\ App.app"
$ identity="User ID" # (pasted from clipboard)
$ codesign --force --verify --verbose --sign "$identity" "$app/Contents/Frameworks/crash_inspector"
$ codesign --force --verify --verbose --sign "$identity" "$app/Contents/Frameworks/nwjs Framework.framework"
$ codesign --force --verify --verbose --sign "$identity" "$app/Contents/Frameworks/nwjs Helper EH.app"
$ codesign --force --verify --verbose --sign "$identity" "$app/Contents/Frameworks/nwjs Helper NP.app"
$ codesign --force --verify --verbose --sign "$identity" "$app/Contents/Frameworks/nwjs Helper.app"
$ codesign --force --verify --verbose --sign "$identity" "$app"
```

## E. Verify Signature

Admin or Member:

- [ ] Go to Terminal in the folder containing your .app
- [ ] Ensure spctl accept apps signed with "Developer ID"
```
$ spctl --list --label "Developer ID"
> 6[Developer ID] P0 allow execute
>   anchor apple generic ... exists
> 7[Developer ID] P0 allow install
>   anchor apple generic ...
$ spctl --enable --rule 6 # matching the rule # above
$ spctl --enable --rule 7 # matching the rule # above
```

And then:
```
$ codesign --verify --verbose=4 "$app"
...
> $app: valid on disk
> $app: satisfies its Designated Requirement
$ spctl --assess --verbose=4 "$app"
> $app: accepted
> source=Developer ID
```
