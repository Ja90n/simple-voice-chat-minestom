# Simple Voice Chat for Minestom

A [Simple Voice Chat](https://modrinth.com/plugin/simple-voice-chat) server implementation for [Minestom](https://github.com/Minestom/Minestom) (protocol version 20), supporting Minecraft 26.2.

This fork of [LooFifteen/simple-voice-chat-minestom](https://github.com/LooFifteen/simple-voice-chat-minestom) combines the group API of [PeyaPeyaPeyang's fork](https://github.com/PeyaPeyaPeyang/simple-voice-chat-minestom)
with the 26.2 support and AES/GCM `Secret` rework of [mudkjp's fork](https://github.com/mudkjp/simple-voice-chat-minestom).

> [!CAUTION]
> This library is feature complete but there is still a potential for bugs, please report all of them [here](https://github.com/Ja90n/simple-voice-chat-minestom/issues)

### Features
- [x] Proximity voice chat
- [x] Sound categories
- [x] Groups (including managed groups and group events)
- [x] Customisation
  - [x] Codec
  - [x] Distance
  - [x] MTU
  - [x] Keepalive
  - [x] Recording

### Installation
The library is available through [JitPack](https://jitpack.io/#Ja90n/simple-voice-chat-minestom).
Use a release tag as the version (the latest is `v1.0.0`), or `main-SNAPSHOT` / a commit hash for the latest development version.
Minestom (`2026.10.07-26.2`) and `slf4j-api` are not bundled, you have to provide them yourself.

<details>
<summary>Gradle (Kotlin)</summary>

```kts
repositories {
    mavenCentral()
    maven("https://jitpack.io")
}

dependencies {
    implementation("net.minestom:minestom:2026.10.07-26.2")
    implementation("com.github.Ja90n:simple-voice-chat-minestom:v1.0.0")
}
```
</details>

<details>
<summary>Gradle (Groovy)</summary>

```groovy
repositories {
    mavenCentral()
    maven { url 'https://jitpack.io' }
}

dependencies {
    implementation 'net.minestom:minestom:2026.10.07-26.2'
    implementation 'com.github.Ja90n:simple-voice-chat-minestom:v1.0.0'
}
```
</details>

<details>
<summary>Maven</summary>

```xml
<repositories>
  <repository>
    <id>jitpack.io</id>
    <url>https://jitpack.io</url>
  </repository>
</repositories>

<dependencies>
  <dependency>
    <groupId>net.minestom</groupId>
    <artifactId>minestom</artifactId>
    <version>2026.10.07-26.2</version>
  </dependency>
  <dependency>
    <groupId>com.github.Ja90n</groupId>
    <artifactId>simple-voice-chat-minestom</artifactId>
    <version>v1.0.0</version>
  </dependency>
</dependencies>
```
</details>

The package is still `dev.lu15.voicechat`, so existing code keeps working after switching the dependency.

### Usage

<details>
<summary>Java Example</summary>

```java
VoiceChat voicechat = VoiceChat.builder("0.0.0.0", 21000) // create a new voice chat instance
        .mtu(1024) // Set the mtu of the voice server. This is used determine the largest size of a packet.
        .groups(true) // Enable groups
        .recording(true) // Allow clients to record
        .publicAddress("voice.example.org:30000") // Set the public address of the voice server. This is used to tell clients where to connect to.
        .enable(); // Start the server
```
</details>

<details>
<summary>Managed groups</summary>

Groups are created by players in the client, but you can also manage them from the server (requires `.groups(true)`):

```java
// create a group (name and password are at most 24 characters), which stays alive when it is empty and is hidden from the group list
// returns null when groups are disabled or the name or password is too long
Group group = voicechat.createManagedGroup("Staff", Group.Type.NORMAL, "secret", true, true);

// put a player in the group, this only works for players with a working voice connection
voicechat.setPlayerManagedGroup(player, group.id(), null);

// remove the player from their group again
voicechat.setPlayerManagedGroup(player, null, null);

// delete the group, this removes all players from it
voicechat.removeManagedGroup(group.id());
```

`PlayerCreateGroupEvent`, `PlayerJoinGroupEvent` and `PlayerLeaveGroupEvent` are called when players create, join or leave a group.
</details>
