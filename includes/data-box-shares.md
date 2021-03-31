---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/05/2020
ms.author: alkohli
ms.openlocfilehash: 5aaf0ce747b14b2fa9f2fcd9a65b774aa7d2db3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87102563"
---
V závislosti na vybraném účtu úložiště vytvoří Data Box až:

* Tři sdílené složky pro každý přidružený účet úložiště GPv1 a GPv2.
* Jednu sdílenou složku pro každou službu Storage úrovně Premium.
* Jednu sdílenou složku pro každý účet úložiště objektů blob.

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. Ve sdílených složkách souborů Azure jsou entitami první úrovně sdílené složky a entitami druhé úrovně soubory.

Následující tabulka uvádí cestu UNC ke sdíleným složkám ve vašem Data Boxu a adresu URL cesty ke službě Azure Storage, ve které jsou data nahraná. Konečnou adresu URL cesty ke službě Azure Storage je možné odvodit z cesty UNC ke sdílené složce.
 
| Objekty blob a soubory | Cesty a adresy URL |
| --------------- | -------------- |
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdíleným složkám: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

