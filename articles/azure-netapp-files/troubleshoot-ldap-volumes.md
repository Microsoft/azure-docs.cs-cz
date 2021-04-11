---
title: Řešení potíží se svazkem LDAP pro Azure NetApp Files | Microsoft Docs
description: Popisuje řešení chybových stavů, které můžete mít při konfiguraci svazků LDAP pro Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498968"
---
# <a name="troubleshoot-ldap-volume-issues"></a>Řešení potíží se svazkem LDAP

Tento článek popisuje řešení chybových podmínek, které můžete mít při konfiguraci svazků LDAP.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>Chyby a řešení pro svazky LDAP

|     Chybové stavy    |     Řešení    |
|-|-|
| Chyba při vytváření svazku SMB s ldapEnabled jako true: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | Nelze vytvořit svazek SMB s povoleným protokolem LDAP. <br> Vytváření svazků SMB se zakázaným protokolem LDAP. |
| Při aktualizaci hodnoty parametru ldapEnabled pro existující svazek došlo k chybě: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  Po vytvoření svazku nelze upravit nastavení možnosti LDAP. <br> Neaktualizujte nastavení možnosti LDAP na vytvořeném svazku. Podrobnosti najdete v tématu [Konfigurace přidání LDAP s rozšířenými skupinami pro přístup ke svazkům NFS](configure-ldap-extended-groups.md) . |
| Při vytváření svazku systému souborů NFS s povoleným protokolem LDAP došlo k chybě: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  K této chybě dochází, protože služba DNS je nedosažitelná. <br> <ul><li> Ověřte, zda jste nakonfigurovali správnou lokalitu (obor webu) pro Azure NetApp Files. </li><li> Důvod, proč je služba DNS nedosažitelná, může být nesprávná IP adresa DNS nebo problémy se sítí. Zkontrolujte IP adresu DNS zadanou v připojení AD a ujistěte se, že je správná. </li><li> Ujistěte se, že se AD a svazek nacházejí ve stejné oblasti a ve stejné virtuální síti. Pokud jsou v různých virtuální sítě, ujistěte se, že je mezi těmito dvěma virtuální sítěy navázán partnerský vztah virtuálních sítí.</li></ul> |
| Při vytváření svazku ze snímku došlo k chybě: <br> `Aggregate does not exist` | Azure NetApp Files nepodporuje zřizování nového svazku s povoleným protokolem LDAP ze snímku, který patří do svazku zakázaného protokolem LDAP. <br> Zkuste z daného snímku vytvořit nový svazek zakázaný protokolem LDAP. |

## <a name="next-steps"></a>Další kroky  

* [Konfigurace Přidání LDAP pomocí rozšířených skupin pro přístup ke svazkům NFS](configure-ldap-extended-groups.md)
* [Vytvoření svazku NFS pro službu Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Vytvoření svazku s duálním protokolem (NFSv3 a protokolu SMB) pro Azure NetApp Files](create-volumes-dual-protocol.md)