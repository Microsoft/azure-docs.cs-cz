---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244624"
---
| Entita                                       | Zásady   |
|----------------------------------------------|-----------------------------------------------------|
| Názvy kontejnerů objektů blob bloku a objektů blob stránky | Musí být platný název DNS, který je dlouhý 3 až 63 znaků. <br>  Musí začínat písmenem nebo číslicí. <br> Může obsahovat jenom malá písmena, číslice a pomlčky (-). <br> Každé pomlčce (-) musí bezprostředně předcházet číslice (0–9) nebo malé písmeno (a–z) a také po ní musí následovat. <br> Po sobě jdoucí pomlčky nejsou povolené v názvech. |
| Názvy sdílených složek pro soubory Azure                  | Stejný jako výše uvedený                                          |
| Názvy souborů a adresář pro soubory Azure     |<li> Zachování případu, velkých a malých písmen a nesmí být delší než 255 znaků. </li><li> Nemůže končit lomítkem (/). </li><li>Pokud je zadán, bude automaticky odebrán. </li><li> Nejsou povolené následující znaky: <code>" \\ / : \| < > * ?</code></li><li> Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. </li><li> Neplatné znaky cesty adresy URL nejsou povoleny. Kód body jako \\uE000 nejsou platné znaky Unicode. Řídicí znaky, jako jsou některé znaky ASCII a Unicode (0x00 0x1F \\u0081, atd.), nejsou povoleny. Pravidla kódování Unicode řetězců v protokolu HTTP/1.1 naleznete v dokumentu RFC 2616 2.2 části: Základní pravidla a v dokumentu RFC 3987. </li><li> Tyto názvy nejsou povoleny: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, tečka (.) a dvě tečky znaků (.).</li>|
| Názvy objektů blob bloku a objektů blob stránky      | </li><li>Názvy objektů blob rozlišují velká a malá písmena a smí obsahovat libovolnou kombinaci znaků. </li><li>Název objektu blob musí mít délku 1 až 1024 znaků. </li><li>Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. </li><li>Počet segmentů cesty, ze kterých se název objektu blob skládá, nesmí překročit 254. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače (třeba lomítko „/“), který odpovídá názvu virtuálního adresáře.</li> |
