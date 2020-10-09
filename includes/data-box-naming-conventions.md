---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "66244624"
---
| Entita                                       | Konvence   |
|----------------------------------------------|-----------------------------------------------------|
| Názvy kontejnerů pro objekt blob bloku a objekt blob stránky | Musí se jednat o platný název DNS, který má délku 3 až 63 znaků. <br>  Musí začínat písmenem nebo číslicí. <br> Může obsahovat jenom malá písmena, číslice a spojovník (-). <br> Každé pomlčce (-) musí bezprostředně předcházet číslice (0–9) nebo malé písmeno (a–z) a také po ní musí následovat. <br> Po sobě jdoucí spojovníky nejsou v názvech povolené. |
| Sdílení názvů souborů Azure                  | Stejný jako výše uvedený                                          |
| Názvy adresářů a souborů pro soubory Azure     |<li> Bez rozlišování velkých a malých písmen a nesmí překročit 255 znaků. </li><li> Nemůže končit lomítkem (/). </li><li>Pokud je tato akce k dispozici, bude automaticky odebrána. </li><li> Nejsou povoleny následující znaky: <code>" \\ / : \| < > * ?</code></li><li> Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. </li><li> Neplatné znaky cesty URL nejsou povoleny. Kódové body, jako je \\ uE000, nejsou platné znaky Unicode. Některé znaky ASCII nebo Unicode, například řídicí znaky (0x00 až 0x1F, \\ u0081 atd.), nejsou také povoleny. Pravidla upravující řetězce Unicode v HTTP/1.1 najdete v dokumentu RFC 2616, oddíl 2,2: základní pravidla a RFC 3987. </li><li> Následující názvy souborů nejsou povoleny: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, tečka (.) a dvě tečky (..).</li>|
| Názvy objektů blob bloku a objektů blob stránky      | </li><li>Názvy objektů blob rozlišují velká a malá písmena a smí obsahovat libovolnou kombinaci znaků. </li><li>Název objektu blob musí mít délku 1 až 1024 znaků. </li><li>Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. </li><li>Počet segmentů cesty, ze kterých se název objektu blob skládá, nesmí překročit 254. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače (třeba lomítko „/“), který odpovídá názvu virtuálního adresáře.</li> |
