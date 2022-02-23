<h1 align="center">Wand-SK - Documentation</h1>
<p align="center">Wand-SK est un skript permettant d'avoir une baguette magique.</p><br />

## 🏹 Plus d'informations ?
Vous souhaitez avoir des informations sur comment télécharger et installer ce code ?
Rendez vous sur cette page : [README.md](https://github.com/Maxime-LEGRAND/Wand-SK/blob/main/README.md)

## 👨‍💻 spells
- **_Cette partie si dessous, va vous permettre de gérer comment marche vos sortilèges._**
```
leftclick with {@item}:
    name of tool = "%{@name}% %{@part}% &eLumos":
        send player title "" with subtitle "&e&oLumos..." for 1 seconds

        launch ball large coloured yellow and white fading to white at player's location with duration 2

    name of tool = "%{@name}% %{@part}% &bRepulso":
        set {_t} to targeted player
        {_t} is set:
            set {_d} to distance between player and {_t}
            {_d} < 13:
                send player title "" with subtitle "&b&oRepulso..." for 1 seconds

                push {_t} upwards at speed 0.8
                push {_t} (direction from player to {_t}) at speed 2.1
                add 1 to y-coordinate of {_t}
            
                drawLine particle redstone, RGB 70, 118, 200, center player, target location at {_t}, id "%player%", rainbowMode false, solid true, density 20, length {_d}, zigZag count 0, height 0, visibleRange 32, displacementXYZ 0, 1.3, 0, pulseDelay 1  
                wait 2 tick
                stopEffect id "%player%"
            else:
                send "&c&lErreur: &cVous êtes trop loin !" to player
        else:
            send "&c&lErreur: &cVous ne visez personne !" to player
```
- **_N'oublier pas d'ajouter vos sortilèges ici (item et nom)._**
```
load:
    delete {spells::*}
    add firework star named "&eLumos" to {spells::*}
    add feather named "&bRepulso" to {spells::*}
```

## 👨‍💻 wand
- **_Cette partie si dessous, va vous permettre d'activer/désactiver et faire fonctionner la baquette magique._**
```
rightclick with {@item}:
    name of tool contains {@name}:
        player isn't sneaking:
            size of {wand::inv::%uuid of player%::*} > 0:
                {wand::status::%uuid of player%} = "on":
                    set {wand::status::%uuid of player%} to "off"

                    clear player's inventory
                    set {_s} to 0
                    loop 36 times:
                        set slot {_s} of player to {wand::stuff::%uuid of player%::%{_s}%}
                        add 1 to {_s}
                    send "&cDésactivation de la baguette !" to player

                else: 

                    set {wand::status::%uuid of player%} to "on"

                    set {_s} to 0
                    loop 36 times:
                        set {wand::stuff::%uuid of player%::%{_s}%} to slot {_s} of player's inventory
                        add 1 to {_s}
                    clear player's inventory
                    set slot 0 of player to {@item} named {@name}

                    set {_s} to 1
                    loop 8 times:
                        {wand::inv::%uuid of player%::%{_s}%} is set:
                            set slot {_s} of player to {wand::inv::%uuid of player%::%{_s}%}
                        else:
                            set slot {_s} of player to barrier named "&cIndisponible"
                        add 1 to {_s}
                    send "&aActivation de la baguette !" to player

                    while true:
                        player is online:
                            {wand::status::%uuid of player%} = "on":
                                player's selected hotbar slot != 0:
                                    player's tool != barrier:
                                        set {_n} to name of tool
                                        set slot 0 of player to {@item} named "%{@name}% &7- %{_n}%"
                                    else:
                                        send "&c&lErreur: &cSortilège indisponible !" to player
                                    set player's selected hotbar slot to slot 0 of player
                                wait tick
                            else:
                                stop
                        else:
                            set {wand::status::%uuid of player%} to "off"
                            stop
            else:
                send "&c&lErreur: &cVous n'avez séléctionné aucun sortilège !" to player
```
- **_Vous pouvez gérer l'inventaire de votre baguette en réalissant un sneak + clique-droit._**
```
rightclick with {@item}:
    name of tool contains {@name}:
        player is sneaking:
            {wand::status::%uuid of player%} = "off":
                size of {wand::spells::%uuid of player%::*} > 0:
                    wand_inv(player)
                else:
                    send "&c&lErreur: &cVous ne métriser encore aucun sort !" to player
            else:
                send "&c&lErreur: &cFonction indisponible en mode combat !" to player
```

## 👨‍💻 wand-inv
- **_Cette partie si dessous, va vous permettre de vous montrez comment fonctionne l'inventaire de la baguette magique._**
```
function wand_inv(p: player):
    set {_u} to uuid of {_p}
    open virtual chest named "&8&lGestion de la Baguette" with 1 rows to {_p}

    set slot 0,1,2,3,4,5,6 and 7 of {_p}'s current inventory to barrier named "&cNon séléctionné"
    set slot 8 of {_p}'s current inventory to orange glass named "&6Retirer les sortilèges"

    set {_s} to 0
    loop 8 times:
        {wand::inv::%{_u}%::%({_s}+1)%} is set:
            set slot {_s} of {_p}'s current inventory to {wand::inv::%{_u}%::%({_s}+1)%}
        else:
            set slot {_s} of {_p}'s current inventory to barrier named "&cNon séléctionné"
        add 1 to {_s}

inventory click:
    {wand::status::%uuid of player%} = "on":
        cancel event
    name of current inventory = "&8&lGestion de la Baguette":
        cancel event

        event-item = orange glass:
            size of {wand::inv::%uuid of player%::*} > 0:
                delete {wand::inv::%uuid of player%::*}
                set slot 0,1,2,3,4,5,6 and 7 of player's current inventory to barrier named "&cNon séléctionné"
                send "&cVous avez retiré vos sortilèges !" to player
            else:
                send "&c&lErreur: &cVous n'avez aucun sortilège de séléctionné !" to player

        event-item = barrier:
            set {wand::pre-inv::%uuid of player%} to ((index of event-slot)+1)
            set {_v::*} to {wand::spells::%uuid of player%::*}

            loop {wand::inv::%uuid of player%::*}:
                remove loop-value from {_v::*}
            size of {_v::*} > 0:
                open virtual chest named "&8&lSortilèges" with 1 rows to player
                wait tick
                set {_s} to 0
                loop {_v::*}:
                    set slot {_s} of player's current inventory to loop-value
                    add 1 to {_s}
            else:
                send "&cErreur: Tous les sortilèges métrisés sont déjà séléctionné !" to player

    else if name of current inventory = "&8&lSortilèges":
        set {wand::inv::%uuid of player%::%{wand::pre-inv::%uuid of player%}%} to event-item
        delete {wand::pre-inv::%uuid of player%}

        set {_n} to name of event-item
        send "&aVous avez ajouté %{_n}% &aà votre inventaire !" to player
        wand_inv(player)
        cancel event
```
