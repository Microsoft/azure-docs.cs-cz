---
title: Vrácení Unity míč kurzu
description: Postup vytvoření klasického Unity vrátit míč hra, který je nezbytný předpoklad pro všechny kurzy Mobile Engagement Unity
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 52d5962645b1408fdba61ec1bf4e4f682b80905b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a id="unity-roll-a-ball"></a>Vytvoření Unity vrátit míč hry
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento kurz vás provede hlavní kroky pro mírně upravenou [Unity vrátit míč kurzu](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial). Tato ukázka hra se skládá z kulovým 'player, objekt, který řídí uživatele aplikace a hry cílem je, shromažďování' collectible objekty podle kolize objektu player se tyto collectible objekty. Toto předpokládá základní znalost prostředí editor Unity. Pokud narazíte na problémy s pak naleznete v úplné kurzu. 

### <a name="setting-up-the-game"></a>Nastavení hra
Následující kroky jsou určeny z [kurzu Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Otevřete **Unity Editor** a klikněte na tlačítko **nový**. 
   
    ![][51] 
2. Zadejte **název projektu** & **umístění**, vyberte **3D** a klikněte na tlačítko **vytvořit projekt**.
   
    ![][52]
3. Uložit scény výchozí právě vytvořené jako součást nového projektu jako s názvem **MiniGame** v rámci nového  **\_scény** ve složce **prostředky** složky:
   
    ![][53]
4. Vytvoření **3D objektu -> roviny** jako přehrávání pole a přejmenujte tento objekt roviny jako **základů**
   
    ![][1]
5. Resetovat pro tuto komponentu transformace **základů** objektu tak, aby se v původu. 
   
    ![][3]
6. Zrušte zaškrtnutí políčka **zobrazit mřížky** z **si nabídky** pro **základů** objektu.
   
    ![][4]
7. Aktualizace **škálování** pro součásti **základů** objekt, který má být [X = 2, Y = 1, Z = 2]. 
   
    ![][5]
8. Přidejte nový **3D objektu -> oblasti** projektu a přejmenování této oblasti objekt jako **Player**. 
   
    ![][6]
9. Vyberte **Player** objekt a klikněte na tlačítko **resetovat transformace** podobné roviny objektu. 
10. Aktualizace **transformace -> Pozice -> souřadnici Y** součásti pro Player Y as 0,5.  
    
    ![][7]
11. Vytvořit novou složku s názvem **materiály** v projektu, kde vytvoříme materiálu pro barevný přehrávač. 
12. Vytvořte novou **materiálu** názvem **pozadí** v této složce. 
    
    ![][8]
13. Aktualizovat barvu materiálu aktualizací **Albedo** vlastnost ho. Můžete vybrat hodnoty RGB [0,32,64]. 
    
    ![][9]
14. Přetáhněte tomto materiálu do zobrazení scény použít barvu, která **základů** objektu. 
    
    ![][10]
15. Nakonec aktualizujte **transformace -> otočení -> Y** na 60 na objekt směrovou Light pro přehlednost. 
    
    ![][12]

### <a name="moving-the-player"></a>Přesunutí player
Následující kroky jsou určeny z [kurzu Unity](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Přidat **RigidBody** součásti na **Player** objektu. 
   
    ![][13]
2. Vytvořit novou složku s názvem **skripty** v projektu. 
3. Klikněte na tlačítko **přidat součást -> nový skript -> C# skript**. Pojmenujte ji **PlayerController**a klikněte na tlačítko **vytvořit a přidat**. Tato akce vytvoří a připojí k objektu Player skript.  
   
    ![][14]
4. Tento skript v části přesunout **skripty** složky v projektu. 
5. Otevřete skript pro úpravy v editoru vaše oblíbené skriptu, aktualizujte kód skriptu následujícím kódem a uložte ho. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Všimněte si, že používá skript výše **rychlost** vlastnost. V editoru Unity aktualizujte vlastnost rychlostí 10.  
   
    ![][15]
7. Stiskněte tlačítko **přehrání** v editoru Unity. Teď by měla být schopné řídit míč pomocí klávesnice a měl by otočit a pohyb. 

### <a name="moving-the-camera"></a>Přesunutí kamera
Následující kroky jsou určeny z [Unity kurzu](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) a bude tie **hlavní fotoaparát** k **Player** objektu. 

1. Aktualizace **Transform.Position** být X = 0, Y = 10.5, Z =-10.  
2. Aktualizace **Transform.Rotation** být X = 45, Y = 0, Z = 0.  
   
    ![][16]
3. Přidat nový skript volá **CameraController** k **MainCamera** a přesunout ji pod složky skriptů. 
   
    ![][17]
4. Otevřete pro úpravy skriptu a do něj přidejte následující kód:
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. V prostředí Unity - přetáhněte proměnnou Player do přihrádky Player pro objekt fotoaparát hlavní tak, aby dva jsou přidruženy jednu na druhou. 
   
    ![][18]
6. Teď Pokud začít přehrávat v editoru Unity a otočení objektu Player míč pak uvidíte fotoaparát následující v pohyb.  

### <a name="setting-up-the-play-area"></a>Nastavení oblasti Play
Následující kroky jsou určeny z [Unity kurzu](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Vytvoříme bariéry, které obaluje základů tak, aby objekt Player míč není odkládací oblast play v jeho přesunu. 

1. Klikněte na tlačítko **vytvořit -> vytvořit prázdnou -> objekt herní** a pojmenujte ji **bariéry**
   
    ![][19]
2. V rámci tohoto objektu bariéry - vytvořit nový **3D objektu -> datové krychle** a pojmenujte ji "Wall – západ". 
   
    ![][20]
3. Aktualizace **transformace -> Pozice** a **transformace -> škálování** pro tento objekt Wall – západ. 
   
    ![][21]
4. Duplicitní wall – západ vytvořit **wall – východ** s aktualizovaný transformace pozice a škálování. 
   
    ![][22]
5. Duplicitní wall – východ, chcete-li vytvořit **wall – sever** s aktualizovaný transformace pozice & škálování. 
   
    ![][23]
6. Duplicitní wall – sever a vytvořte **wall – jih** s aktualizovaný transformace pozice & škálování. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>Vytváření Collectible objektů
Následující kroky jsou určeny z [Unity kurzu](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Vytvoříme některé atraktivní vypadající objekty, které tvoří sada collectible objekty, které se musí objekt Player míč ke shromažďování podle kolize s nimi. 

1. Vytvořte novou **3D objektu datové krychle** a pojmenujte ji vyzvednutí. 
2. Upravit **transformace -> otočení** & **transformace -> škálování** výstupního objektu. 
   
    ![][25]
3. Vytvořte a připojte **nový C# skript** názvem **Rotator** do výstupního objektu. Ujistěte se, že se skript vložit ve složce skripty. 
   
    ![][26]
4. Otevřete tento skript pro úpravy a aktualizovat ji jako následující: 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. Nyní průchodu režimu Play v editoru Unity a zobrazit vaše výstupního objektu otáčení na ose.
6. Vytvořit novou složku s názvem **Prefabs** 
   
    ![][27]
7. Přetáhněte **vyzvednutí** objektu a umístí jej do složky Prefabs.
   
    ![][28]
8. Vytvořte novou **herní prázdný objekt** názvem **Pickups**. Resetovat své pozici počátek a poté přetáhněte výstupního objektu v rámci této herní objektu.  
   
    ![][29]
9. Duplicitní **vyzvednutí** objektu a šířit na **základů** objektu kolem **Player** objekt aktualizací **X & ZnaTransform.Position** hodnoty správně. 
   
    ![][30]
10. Vytvoření **nové materiály** názvem **vyzvednutí** a aktualizovat ji jako červená barva aktualizací **Albedo vlastnost** podobná co jsme to udělali pro aktualizaci základů objektu. 
    
    ![][31]
11. Platí pro všechny objekty 4 výstupní materiál.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Shromažďování výstupní objekty
Následující kroky jsou určeny z [Unity kurzu](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Přehrávač budeme aktualizovat tak, aby bylo možné na "shromažďování' výstupní objekty podle kolize s nimi. 

1. Otevřete **PlayerController** skript připojen k objektu Player pro úpravy a aktualizace pro následující:  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Vytvořte novou **značka** názvem **vyberte si** (musí se shodovat co je ve skriptu)  
   
    ![][33]
   
    ![][34]
3. Použít **značky** k objektu Prefab vyzvednutí. 
   
    ![][35]
4. Povolit **IsTrigger** zaškrtávací políčko pro objekt Prefab.
   
    ![][36]
5. Přidáte pevné text do vyzvednutí Prefab objektu. Pro optimalizaci výkonu aktualizujeme dynamické collider statické collider, který jsme použili. 
   
    ![][37]
6. Nakonec zkontrolujte **IsKinematic** vlastnost prefab objektu. 
   
    ![][38]
7. Stiskněte tlačítko **přehrání** v Unity editoru a bude možné přehrát toto **vrátit míč** herní přesunutím objektu Player pro vstup směr pomocí kláves na klávesnici. 

### <a name="updating-the-game-for-mobile-play"></a>Aktualizace hry pro mobilní play
Výše uvedené oddíly se dospělo k závěru základní kurzem z Unity. Nyní jsme upraví hra, aby bylo mobilní zařízení popisný. Upozorňujeme, že jsme vstup z klávesnice pro hra, pokud pro testování. Nyní jsme ji upravovat tak, aby přehrávač jsme můžete řídit pomocí pohybu telefonu, tj. pomocí zrychlení jako vstup. 

Otevřete **PlayerController** skript pro úpravy a aktualizovat **FixedUpdate** metodu použít pohybu z zrychlení k přesunutí objektu Player. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

V tomto kurzu se ukončí základní herní vytvoření s Unity a můžete nasadit na zařízení podle svého výběru ve hře. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













