---
title: Konfigurace nastavení služby Windows Update pro řešení Update Management služby Azure Automation
description: V tomto článku se dozvíte, jak nakonfigurovat web Windows Update nastavení pro práci s Update Management Azure Automation.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: a1f95ca856223628974a9519b7c4811bde43965e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92222158"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Konfigurace nastavení služby Windows Update pro řešení Update Management služby Azure Automation

Azure Automation Update Management spoléhá na [klienta web Windows Update](/windows/deployment/update/windows-update-overview) ke stažení a instalaci aktualizací Windows. K dispozici jsou určitá nastavení, která používá klient web Windows Update při připojování k Windows Server Update Services (WSUS) nebo web Windows Update. Mnohé z těchto nastavení se dají spravovat pomocí:

- Editor místních zásad skupiny
- Zásady skupiny
- PowerShell
- Přímá úprava registru

Update Management respektuje mnoho nastavení určených k řízení web Windows Update klienta. Pokud použijete nastavení k povolení aktualizací jiných než Windows, Update Management tyto aktualizace taky spravovat. Pokud chcete povolit stahování aktualizací před tím, než dojde k nasazení aktualizace, může být nasazení aktualizace rychlejší, efektivnější a méně pravděpodobně větší než časový interval pro správu a údržbu.

Další doporučení týkající se nastavení služby WSUS v předplatném Azure a bezpečném udržování virtuálních počítačů s Windows najdete v přehledu [Plánování nasazení pro aktualizaci virtuálních počítačů s Windows v Azure pomocí služby WSUS](/azure/architecture/example-scenario/wsus/).

## <a name="pre-download-updates"></a>Předběžné stažení aktualizací

Pokud chcete automaticky stahovat aktualizace, aniž byste je museli instalovat, můžete pomocí Zásady skupiny [nakonfigurovat nastavení automatických aktualizací](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na 3. Toto nastavení umožňuje stáhnout požadované aktualizace na pozadí a upozorní vás, že jsou aktualizace připravené k instalaci. Tímto způsobem Update Management zůstane v řízení plánů, ale umožňuje stahovat aktualizace mimo Update Management časový interval pro správu a údržbu. Toto chování brání `Maintenance window exceeded` chybám v Update Management.

Toto nastavení můžete povolit v PowerShellu:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Konfigurace nastavení restartování

Klíče registru uvedené v [části Konfigurace automatických aktualizací úpravou registru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) a [klíčů registru, které slouží ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , můžou způsobit restartování počítačů i v případě, že v nastavení **nasazení aktualizace** neurčíte **nikdy restartování** . Nakonfigurujte tyto klíče registru tak, aby nejlépe vyhovovaly vašemu prostředí.

## <a name="enable-updates-for-other-microsoft-products"></a>Povolit aktualizace pro ostatní produkty Microsoftu

Ve výchozím nastavení je klient web Windows Update nakonfigurovaný tak, aby poskytoval aktualizace jenom pro Windows. Pokud **při aktualizaci nastavení systému Windows povolíte aktualizace pro další produkty společnosti Microsoft** , obdržíte také aktualizace pro další produkty, včetně oprav zabezpečení pro Microsoft SQL Server a další software společnosti Microsoft. Tuto možnost můžete nakonfigurovat, pokud jste stáhli a zkopírovali nejnovější [soubory šablon pro správu](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) , které jsou k dispozici pro Windows 2016 a novější.

Pokud máte počítače s Windows Serverem 2012 R2, nemůžete toto nastavení konfigurovat prostřednictvím Zásady skupiny. Na těchto počítačích spusťte následující příkaz PowerShellu:

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>Vytvoření nastavení konfigurace služby WSUS

Update Management podporuje nastavení služby WSUS. Můžete určit zdroje pro vyhledávání a stahování aktualizací podle pokynů v tématu [určení umístění intranetové Microsoft Update služby](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Ve výchozím nastavení je klient web Windows Update nakonfigurovaný tak, aby stahoval aktualizace z web Windows Update. Pokud zadáte server WSUS jako zdroj pro počítače, pokud aktualizace nejsou schváleny ve službě WSUS, nasazení aktualizace se nepovede. 

Chcete-li omezit počítače na službu interní aktualizace, nastavte [Nepřipojujte se k žádným web Windows Update internetovým umístěním](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations).

## <a name="next-steps"></a>Další kroky

Naplánujte nasazení aktualizace podle pokynů v tématu [Správa aktualizací a oprav pro vaše virtuální počítače](manage-updates-for-vm.md).
