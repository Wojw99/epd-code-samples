# Evil Plays Dice - code samples
The scripts I wrote for EPD can be grouped basically in three ways: work automation, engine expansion, helpers. Below are some examples.

## 1. Work automation 
```javascript
WW_MessCore.prototype.setCurrentDialog = function (dialog) {
    this._currentDialog = dialog;
}

WW_MessCore.prototype.showMessage = function (event, messageId) {
    if ($gameMessage.isBusy()) {
        return;
    }

    let messageObj = this._currentDialog.find(mess => mess.id === messageId)
    let actor = $gameActors.actor(messageObj.actor)
    let wrappedText = messageObj.text.wrapWords(this._maxWordsInLine)
    let name = messageObj.name

    if (name === "" || name === null || name === undefined) {
        name = actor.name()
    }
    if (actor.id === 1) {
        name = this.getPlayerName()
    }

    $gameMessage.setFaceImage(actor.faceName(), actor.faceIndex())
    $gameMessage.setBackground(1)
    $gameMessage.add(`\\fb${name}\\fr \\af[${messageObj.actor}]\n${wrappedText}`)

    event.setWaitMode("message")
}
```
Above methods of custom class WW_MessCore really helped me in implementing dialogs to the game. By default in RPG Maker MV, when we don't use code, we have to specify name, face and text for actor's message every time when we want to show it. In above simple method I am getting this information from game database. Thanks to it I was able to modify actors data without necessity to modify it in every message separately. This helped my also in changing languages functionality. 

## 2. Engine expansion
```javascript
WW_MapUI.prototype.drawPortraits = function() {
    let padding = 10
    let portraitWidth = 56
    let portraitHigh = 91
    let screenWidth = 1280
    let portraitsX = screenWidth - portraitWidth - padding
    let members = $gameParty.allMembers()
    
    for(i = 0; i < members.length; i++) {
        let pictureName = this.getUiPictureName(members[i]._actorId)
        let positionY = portraitHigh * i + padding * (i + 1)

        $gameScreen.showPicture(this.portraitIdStart + i, pictureName, 0, portraitsX, positionY, 100, 100, 255, 0)
        $gameScreen.showPicture(this.portraitBackgroundIdStart + i, WW_Constants.UI_PORTRAIT_BACKGROUND, 0, portraitsX - 5, positionY - 5, 100, 100, 255, 0)

        let hpScaleX = parseInt(members[i].hpRate() * 100)
        $gameScreen.showPicture(this.hpIdStart + i, WW_Constants.UI_HP_BAR, 0, portraitsX, positionY + portraitHigh - 14, hpScaleX, 100, 255, 0)
        let mpScaleX = parseInt(members[i].mpRate() * 100)
        $gameScreen.showPicture(this.mpIdStart + i, WW_Constants.UI_MP_BAR, 0, portraitsX, positionY + portraitHigh - 7, mpScaleX, 100, 255, 0)
    }
}
```
This is method for drawing party member faces on the screen. It's a part of code that I used to modify UI and bring it closer to look of classic cRPGs. It is called in several places, including when user presses button of changing formation. 

## 3. Helpers 
```javascript
String.prototype.wrapWords = function(maxWordsInLine) {
    if (this.length < maxWordsInLine) { 
        return this
    }

    var wrappedText = this
    var currChar = maxWordsInLine

    while(currChar < wrappedText.length & currChar > 0) {
        while(wrappedText[currChar] !== " " && currChar > 0) {
            currChar -= 1;
        }
        
        if (currChar > 0) {
            wrappedText = wrappedText.replaceAt(currChar, "\n")
            currChar += maxWordsInLine
        }
    }

    return wrappedText
}

function getRandomInt(min, max) {
    min = Math.ceil(min);
    max = Math.floor(max);
    return Math.floor(Math.random() * (max - min) + min); 
}
```
I was using also helper methods that were coming to me with aid in different part of code.
