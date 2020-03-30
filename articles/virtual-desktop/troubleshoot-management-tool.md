---
title: Nástroj pro správu virtuálních desktopů Windows – Azure
description: Řešení problémů s nástrojem pro správu virtuální plochy systému Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127488"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Řešení potíží s nástrojem pro správu Windows Virtual Desktopu

Tento článek popisuje problémy, které mohou nastat při nasazování nástroje pro správu virtuální plochy systému Windows a jak je opravit.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Chyba: Služby nástrojů pro správu jsou nakonfigurovány, ale automatické nastavení se nezdaří.

Pokud úspěšně nastavíte služby pro nástroj pro správu, ale automatické nastavení se nezdaří, zobrazí se tato chybová zpráva:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

To obvykle znamená jednu z následujících dvou věcí:

- Uživatel má oprávnění vlastníka k předplatnému a globálnímu správci na úrovni tenanta, ale nemůže se přihlásit k Azure.
- Nastavení účtu uživatele má povoleno vícefaktorové ověřování.

Chcete-li tento problém vyřešit:

1. Ujistěte se, že uživatel, který jste vytvořili pro hlavní název uživatele služby Azure Active Directory, má úroveň předplatného Přispěvatel.
2. Přihlaste se k <portal.azure.com> pomocí účtu UPN, zkontrolujte nastavení účtu a ujistěte se, že není zapnuté vícefaktorové ověřování. Pokud je zapnutá, vypněte ji.
3. Navštivte stránku Souhlas virtuální plochy windows a ujistěte se, že server a klientské aplikace mají souhlas.
4. Zkontrolujte kurz [nasazení nástroje pro správu,](manage-resources-using-ui.md) pokud problém přetrvává, a znovu nasaďte nástroj.

## <a name="error-job-with-specified-id-already-exists"></a>Chyba: Úloha se zadaným ID již existuje.

Pokud se uživateli zobrazí chybová zpráva Úloha se zadaným ID již existuje, je to proto, že při nasazování šablony neposkytl jedinečný název v parametru Název aplikace.

Chcete-li tento problém vyřešit, znovu nasaďte nástroj pro správu s vyplněným parametrem "Název aplikace".

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Výzva k odložení souhlasu při otevírání nástroje pro správu

Při nasazení nástroje pro správu se výzva k souhlasu nemusí okamžitě otevřít. To znamená, že načtení služby Azure Web App trvá déle než obvykle. Výzva by se měla zobrazit po dokončení načítání Azure Web.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>Uživatel nemůže nasadit nástroj pro správu v oblasti USA – východ.

Pokud zákazník nastaví oblast na východní USA, nemůže nasadit nástroj pro správu.

Chcete-li tento problém vyřešit, nasaďte nástroj pro správu v jiné oblasti. Opětovné nasazení nástroje v jiné oblasti by nemělo mít vliv na uživatelské prostředí.

## <a name="next-steps"></a>Další kroky

- Informace o trasách eskalace naleznete v [přehledu řešení potíží, zpětné vazbě a podpoře](troubleshoot-set-up-overview.md).
- Přečtěte si, jak nahlásit problémy s nástroji virtuální plochy systému Windows na [webu ARM Templates for Remote Desktop Services](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Přehled řešení potíží s virtuální plochou Windows a traseskalace najdete [v tématu Přehled řešení potíží, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Informace o nasazení nástroje pro správu naleznete v [tématu Nasazení nástroje pro správu](manage-resources-using-ui.md).