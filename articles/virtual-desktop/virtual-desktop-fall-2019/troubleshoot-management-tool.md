---
title: Nástroj pro správu virtuálních počítačů s Windows – Azure
description: Řešení potíží s nástrojem pro správu virtuálních počítačů s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb9206d2c2c15c036b0cf1184b79ed126966eb3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614795"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Řešení potíží s nástrojem pro správu Windows Virtual Desktopu

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows.

Tento článek popisuje problémy, které mohou nastat při nasazení nástroje pro správu virtuálních počítačů s Windows a jejich řešení.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Chyba: nakonfigurované služby nástrojů pro správu, ale automatizovaná instalace se nezdařila

Když jste úspěšně nastavili služby pro nástroj pro správu, ale automatizovaná instalace se nezdaří, zobrazí se tato chybová zpráva:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

To obvykle znamená jednu z následujících dvou věcí:

- Uživatel má oprávnění vlastníka ke svému předplatnému a globálnímu správci na úrovni tenanta, ale nemůže se přihlásit k Azure.
- U nastavení účtu uživatele je povoleno ověřování Multi-Factor Authentication.

Chcete-li tento problém vyřešit:

1. Ujistěte se, že uživatel, který jste vytvořili pro Azure Active Directory hlavní název uživatele, má úroveň předplatného přispěvatele.
2. Přihlaste se, abyste <portal.azure.com> s účtem hlavního názvu uživatele (UPN) a zkontrolovali nastavení účtu a ujistěte se, že vícefaktorové ověřování není zapnuté. Pokud je tato funkce zapnutá, vypněte ji.
3. Navštivte stránku s vyjádřením souhlasu s virtuálním počítačem s Windows a ujistěte se, že server a klientské aplikace mají souhlas.
4. Pokud potíže potrvají a znovu nasadíte, přečtěte si kurz [nasazení nástroje pro správu](manage-resources-using-ui.md) .

## <a name="error-job-with-specified-id-already-exists"></a>Chyba: úloha se zadaným ID už existuje.

Pokud se uživateli zobrazí chybová zpráva s oznámením, že úloha se zadaným ID už existuje, "je důvodem to, že při nasazování šablony nezadala jedinečný název v parametru" název aplikace ".

Chcete-li tento problém vyřešit, znovu nasaďte Nástroj pro správu s vyplněným parametrem "název aplikace".

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Po otevření nástroje pro správu se zobrazí výzva ke zpožděnému souhlasu.

Když nasadíte Nástroj pro správu, výzva k vyjádření souhlasu se nemusí hned otevřít. To znamená, že se zátěž služby Azure Web App trvá déle než obvykle. Po dokončení načítání Azure web by se měla zobrazit výzva.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>Uživatel nemůže nasadit nástroj pro správu v oblasti Východní USA.

Pokud zákazník nastaví oblast, která se má Východní USA, nepůjde nasadit nástroj pro správu.

Pokud to chcete opravit, nasaďte Nástroj pro správu v jiné oblasti. Opětovné nasazení nástroje v jiné oblasti by nemělo mít vliv na uživatelské prostředí.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o sledování eskalace při [řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview-2019.md).
- Naučte se nahlásit problémy s nástroji pro virtuální počítače s Windows na [šablonách ARM pro vzdálenou plochu](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Informace o tom, jak nasadit nástroj pro správu, najdete v tématu [nasazení nástroje pro správu](manage-resources-using-ui.md).