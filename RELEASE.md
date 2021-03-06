## KeystoneML release process

### Snapshot Release

1. We publish KeystoneML releases and snapshots to sonatype repository https://oss.sonatype.org/. To
create a release you will first need to create a SonaType account.

2. Create and distribute a gpg key if you don't have one. The steps to do that are:
```
  gpg --gen-key # Follow the prompts to create a key
  # Publish the key to a keyserver. If this doesn't work go to https://sks-keyservers.net/i/
  # and paste the output of gpg --armor --export <email-id-used-in-previous-step>
  gpg --send-keys 
``` 

3. Add `sbt-pgp` and `sbt-sonatype` to your plugins at `~/.sbt/0.13/plugins/gpg.sbt`
```
addSbtPlugin("com.jsuereth" % "sbt-pgp" % "1.0.0")

addSbtPlugin("org.xerial.sbt" % "sbt-sonatype" % "1.1")
```

4. Add your Sonatype credentials to `~/.sbt/0.13/sonatype.sbt` 
credentials += Credentials("Sonatype Nexus Repository Manager",
                           "oss.sonatype.org",
                           "<userame>",
                           "<password>")

5. Run `sbt/sbt +publishSigned`. This should be sufficient for a snapshot release. To use a snapshot
release users will need to add the Sonatype snapshot repository to their list of resolvers. In SBT
it looks like
```scala
resolvers ++= Seq(
  "Sonatype staging" at "https://oss.sonatype.org/content/repositories/snapshots",
  ... // other resolvers
  )
```

## Full Release

1. Update the build.sbt and the Makefile to reference the new version

2. Cut a release branch for major version releases
```
git checkout -b branch-v[major_version_here]
git add build.sbt
git add Makefile
git commit -m "Cut release branch for [major_version_here]"
```

3. Tag the specific commit of the release branch to be released with its version, and push to the upstream AMPLab
KeystoneML repo.
```
git tag v[full_version_here]
git push [upstream_keystoneml_reference] branch-v[major_version_here] --tags
```

4. We publish KeystoneML releases and snapshots to sonatype repository https://oss.sonatype.org/. To
create a release you will first need to create a SonaType account.

5. Create and distribute a gpg key if you don't have one. The steps to do that are:
```
  gpg --gen-key # Follow the prompts to create a key
  # Publish the key to a keyserver. If this doesn't work go to https://sks-keyservers.net/i/
  # and paste the output of gpg --armor --export <email-id-used-in-previous-step>
  gpg --send-keys
```

6. Add `sbt-pgp` and `sbt-sonatype` to your plugins at `~/.sbt/0.13/plugins/gpg.sbt`
```
addSbtPlugin("com.jsuereth" % "sbt-pgp" % "1.0.0")

addSbtPlugin("org.xerial.sbt" % "sbt-sonatype" % "1.1")
```

7. Add your Sonatype credentials to `~/.sbt/0.13/sonatype.sbt`
credentials += Credentials("Sonatype Nexus Repository Manager",
                           "oss.sonatype.org",
                           "<userame>",
                           "<password>")

8. Run `sbt/sbt +publishSigned` to deploy your artifact to a staging repository on Sonatype.

9. Run `sbt/sbt sonatypeRelease` to close and release the repository.
If that doesn't work, follow [these instructions](http://central.sonatype.org/pages/releasing-the-deployment.html) to
close and release the staging repository. It will soon after be synced to the Central Repository.
