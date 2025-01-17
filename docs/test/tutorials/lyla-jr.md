# Pickle Party
### @explicitHints true


## Pickle Party Intro @showdialog

**Ready to code a pickle party?**

![Pickle Party Game](/static/tutorials/lyla/lyla.gif "Characters chasing pickle slices." )



## {Step 2}

**Take a look at the game window**

![Lyla on the screen](/static/tutorials/lyla/lyla.png "Lyla is alone on the screen." )

You should see Lyla on the screen.



## {Step 3}

**Click the circles to add other players**

![The circles allow you to control other players](/static/tutorials/lyla/circles.gif "Click the circles to control other players." )





## {Step 4}

**You can move the selected player with the arrow keys**

![The arrow keys move whichever player you have selected](/static/tutorials/lyla/arrows.gif "The game window is the same color as the player circle." )





## {Step 5}

**Let's add pickles for them to chase!**

![Add a flying pickle with a new block](/static/tutorials/lyla/pickle.gif "Choose the pickle from the gallery" )

#### ~ tutorialhint

```blocks
game.onUpdateInterval2(1, function () {
    //@highlight
    sprites.sendFlying(lyla_imgs.picklechip)
})
```




## {Step 6}

**Play the game!**

![Choose a character and chase pickles](/static/tutorials/lyla/play.gif "You get points for pickles" )

Use the arrow keys to catch pickles.



## {Step 7}

**We need a way to win**

Tell the computer what to do when someone scores 15 points.

![Grab the player scores container](/static/tutorials/lyla/score.gif "When a player scores 15 points, the code inside will run." )

#### ~ tutorialhint

```blocks
//@highlight
info.onScore2(15, function () {})
```




## {Step 8}

**Add the _game over_ block**

![Add the game over block to the new container](/static/tutorials/lyla/game-over.gif "This block tells the computer that the first to 15 wins" )

#### ~ tutorialhint

```blocks
info.onScore2(15, function () {
    //@highlight
    game.gameOver(true)
})
```




## {Step 9}

**Play until you win!**





## {Step 10}

**Let's make it fancy**

Grab the block that runs code when the game starts.

![Add the "on start" container](/static/tutorials/lyla/on-start.gif "This block tells the computer to run the code inside when the game starts" )

#### ~ tutorialhint

```blocks
//@highlight
scene.setBackgroundImage(img`.`)
```







## {Finale}

**🥒 Excellent 🥒**

You have finished your multiplayer experience!

When you're ready, sign-in, then select **Done** to start an online multiplayer session with up to three friends.

**Who will be the first to score 20 points?**


```blockconfig.global
info.onScore2(15, function () { })
game.holdText("Press (A) when everyone is ready to start the game")
```



```template
game.onUpdateInterval2(1, function () { })
```


```ghost
info.onScore2(15, function () {
    game.gameOver(true)
})
scene.setBackgroundImage(lyla_imgs.diner)
game.holdText("Press (A) when everyone is ready to start the game")
music.play(music.createSong(lyla_imgs.countdown), music.PlaybackMode.UntilDone)
game.onUpdateInterval2(1, function () {
    sprites.sendFlying(lyla_imgs.picklechip)
})
```



```package
multiplayer
lyla_imgs=github:kiki-lee/lyla_imgs#v0.0.3
```


```customts
mp.onControllerEvent(ControllerEvent.Connected, function (thisPlayer) {
    if (mp.getPlayerProperty(thisPlayer, mp.PlayerProperty.Number) <= characters.length) {
        pizza.setPlayersWith(characters, mp.getPlayerProperty(thisPlayer, mp.PlayerProperty.Number))
    }
})
sprites.onOverlap(SpriteKind.Player, SpriteKind.Projectile, function (sprite, otherSprite) {
    if (!(mp.isButtonPressed(mp.getPlayerBySprite(sprite), mp.MultiplayerButton.A))) {
        sprites.destroy(otherSprite, effects.disintegrate, 100)
        mp.changePlayerStateBy(mp.getPlayerBySprite(sprite), MultiplayerState.score, 1)
    }
})
sprites.onOverlap(SpriteKind.Player, SpriteKind.Player, function (sprite, otherSprite) {
    if (mp.isButtonPressed(mp.getPlayerBySprite(sprite), mp.MultiplayerButton.A)) {
        scene.cameraShake(4, 500)
        pizza.bumpSprite(sprite, otherSprite)
        mp.changePlayerStateBy(mp.getPlayerBySprite(otherSprite), MultiplayerState.score, -1)
    }
    if (mp.isButtonPressed(mp.getPlayerBySprite(otherSprite), mp.MultiplayerButton.A)) {
        scene.cameraShake(4, 500)
        pizza.bumpSprite(otherSprite, sprite)
        mp.changePlayerStateBy(mp.getPlayerBySprite(sprite), MultiplayerState.score, -1)
    }
})


let characters = [lyla_imgs.lyla, lyla_imgs.everett, lyla_imgs.stu]
pizza.setPlayersWith(characters, 1)

namespace game {
    /**
     * Run code on an interval of time. This executes before game.onUpdate()
     * @param body code to execute
     */
    //% group="Gameplay"
    //% help=game/on-update-interval weight=99 afterOnStart=true
    //% blockId=gameinterval2 block="every $period second(s)"
    //% period.defl=1
    //% blockAllowMultiple=1
    export function onUpdateInterval2(period: number, a: () => void): void {
        period = period * 1000;
        if (!a || period < 0) return;
        let timer = 0;
        game.eventContext().registerFrameHandler(scene.UPDATE_INTERVAL_PRIORITY, () => {
            const time = game.currentScene().millis();
            if (timer <= time) {
                timer = time + period;
                a();
            }
        });
    }



    //% blockId=hold_text block="hold text $msg"
    //% msg.defl = "Press (A) when everyone is ready to start the game"
    //% img.shadow=screen_image_picker
    export function holdText(msg: string) {
        game.showLongText(msg, DialogLayout.Full)
    }



}

namespace sprites {

    export enum Plyrs {
        //% block="Player 1"
        One = 0,
        //% block="Player 2"
        Two = 1,
        //% block="Player 3"
        Three = 2,
        //% block="Player 4"
        Four = 3
    }



    /**
     * Run code on an interval of time. This executes before game.onUpdate()
     * @param body code to execute
     */
    //% blockId=assign_player_image block="$thing = $img"
    //% thing.defl = Plyrs.One
    //% img.shadow=screen_image_picker
    export function assignPlayerImg(thing: Plyrs, img:Image) {
        characters[thing] = img
        pizza.setPlayersWith(characters, thing+1)
    }


    /**
     * Run code on an interval of time. This executes before game.onUpdate()
     * @param body code to execute
     */
    //% blockId=send_flying block="add flying $thing"
    //% thing.shadow=screen_image_picker
    export function sendFlying(thing:Image){
        let projectile2 = sprites.createProjectileFromSide(thing, randint(-100, 100), randint(-100, 100))
    }
}


//% color=#b79900 icon="\uf1ce"
namespace pizza {

    // Make sure not to remove later player when earlier player tested
    export let playersConnected = 0;


    //% blockId=set_players
    //% block="set game for $num player(s) with $list"
    //% num.defl=1
    //% list.shadow=variables_get
    //% list.defl=characters
    export function setPlayersWith(list: Image[], num: number) {
        if (num >= pizza.playersConnected) {
            pizza.playersConnected = num;
            sprites.destroyAllSpritesOfKind(SpriteKind.Player)
            let xloc = [
                40,
                120,
                40,
                120
            ]
            let yloc = [
                30,
                30,
                90,
                90
            ]
            for (let index = 0; index <= num - 1; index++) {
                mp.setPlayerSprite(mp.getPlayerByIndex(index), sprites.create(list[index], SpriteKind.Player))
                mp.getPlayerSprite(mp.getPlayerByIndex(index)).setStayInScreen(true)
                mp.getPlayerSprite(mp.getPlayerByIndex(index)).setPosition(xloc.shift(), yloc.shift())
                mp.getPlayerSprite(mp.getPlayerByIndex(index)).z = 1000
                mp.moveWithButtons(mp.getPlayerByIndex(index))
            }
        }
    }

    //% blockId=bump_sprite
    //% block="$thisSprite bump $thatSprite"
    //% thisSprite.shadow=variables_get
    //% thisSprite.defl=sprite
    //% thatSprite.shadow=variables_get
    //% thatSprite.defl=otherSprite
    export function bumpSprite(thisSprite: Sprite, thatSprite: Sprite) {
        thatSprite.setPosition((thisSprite.x + 80) % 160, thisSprite.y)
    }
}

namespace info {
    /**
     * Runs code once each time a player's score reaches a given value.
     * @param score The score to check for, eg: 100
     * @param handler The code to run when the score is reached
     */
    //% blockId=mp_onScore2
    //% block="on player scores $score"
    //% score.defl=20
    //% blockGap=8
    //% help=multiplayer/on-score
    //% parts="multiplayer"
    export function onScore2(score: number, handler: () => void) {
        mp._mpstate().onReachedScore(score, handler);
    }
}

```

```simtheme
{
    "palette": [
        "#000000",
        "#FFFFFF",
        "#FF2121",
        "#D26A9E",
        "#F9C890",
        "#FDFF70",
        "#8E2EC4",
        "#408325",
        "#135847",
        "#10CCE5",
        "#95D6B1",
        "#A4839F",
        "#693C16",
        "#E5CDC4",
        "#A46C46",
        "#000000"
    ]
}
```


```assetjson
{
  "assets.json": "",
  "images.g.jres": "{\n    \"EM~exxT809P9NBm_MJip\": {\n        \"data\": \"003c000408010204001c00100500640000041e000004000000000000000000000000000a000004120000000400012408000c00012410001400012407001c00020a006400f401640000040000000000000000000000000000000003060018001c000124\",\n        \"mimeType\": \"application/mkcd-song\",\n        \"displayName\": \"countdown\",\n        \"namespace\": \"mySongs.\"\n    },\n    \"*\": {\n        \"mimeType\": \"image/x-mkcd-f4\",\n        \"dataEncoding\": \"base64\",\n        \"namespace\": \"myImages\"\n    }\n}",
  "images.g.ts": "// Auto-generated code. Do not edit.\nnamespace myImages {\n\n    helpers._registerFactory(\"image\", function(name: string) {\n        switch(helpers.stringTrim(name)) {\n\n        }\n        return null;\n    })\n\n    helpers._registerFactory(\"animation\", function(name: string) {\n        switch(helpers.stringTrim(name)) {\n\n        }\n        return null;\n    })\n\n    helpers._registerFactory(\"song\", function(name: string) {\n        switch(helpers.stringTrim(name)) {\n            case \"EM~exxT809P9NBm_MJip\":\n            case \"countdown\":return hex`003c000408010204001c00100500640000041e000004000000000000000000000000000a000004120000000400012408000c00012410001400012407001c00020a006400f401640000040000000000000000000000000000000003060018001c000124`;\n        }\n        return null;\n    })\n\n}\n// Auto-generated code. Do not edit.\n",
  "main.blocks": "",
  "main.ts": "\n",
  "pxt.json": "{\n    \"name\": \"TMNT_Music\",\n    \"description\": \"\",\n    \"dependencies\": {\n        \"device\": \"*\"\n    },\n    \"files\": [\n        \"main.blocks\",\n        \"main.ts\",\n        \"assets.json\",\n        \"images.g.jres\",\n        \"images.g.ts\",\n        \"tilemap.g.jres\",\n        \"tilemap.g.ts\"\n    ],\n    \"targetVersions\": {\n        \"branch\": \"v1.12.30\",\n        \"tag\": \"v1.12.30\",\n        \"commits\": \"https://github.com/microsoft/pxt-arcade/commits/33228b1cc7e1bea3f728c26a6047bdef35fd2c09\",\n        \"target\": \"1.12.30\",\n        \"pxt\": \"8.5.41\"\n    },\n    \"preferredEditor\": \"tsprj\",\n    \"palette\": [\n        \"#000000\",\n        \"#FFFFFF\",\n        \"#FF2121\",\n        \"#DFDDDE\",\n        \"#FF8135\",\n        \"#FFF609\",\n        \"#8E2EC4\",\n        \"#408325\",\n        \"#003FAD\",\n        \"#87F2FF\",\n        \"#EBF8F9\",\n        \"#A4839F\",\n        \"#F9F6E1\",\n        \"#E5CDC4\",\n        \"#91463d\",\n        \"#000000\"\n    ]\n}\n",
  "tilemap.g.jres": "{\n    \"transparency16\": {\n        \"data\": \"hwQQABAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA==\",\n        \"mimeType\": \"image/x-mkcd-f4\",\n        \"tilemapTile\": true\n    },\n    \"*\": {\n        \"mimeType\": \"image/x-mkcd-f4\",\n        \"dataEncoding\": \"base64\",\n        \"namespace\": \"myTiles\"\n    }\n}",
  "tilemap.g.ts": "// Auto-generated code. Do not edit.\nnamespace myTiles {\n    //% fixedInstance jres blockIdentity=images._tile\n    export const transparency16 = image.ofBuffer(hex``);\n\n    helpers._registerFactory(\"tile\", function(name: string) {\n        switch(helpers.stringTrim(name)) {\n            case \"transparency16\":return transparency16;\n        }\n        return null;\n    })\n\n}\n// Auto-generated code. Do not edit.\n"
}
```
