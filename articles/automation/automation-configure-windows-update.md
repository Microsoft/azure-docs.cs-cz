---
title: Konfigurace nastavení web Windows Update pro práci s Azure Update Management
description: Tento článek popisuje nastavení web Windows Update, která konfigurujete pro práci s Azure Update Management.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79279023"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Konfigurace nastavení web Windows Update pro Update Management

Azure Update Management spoléhá na [klienta web Windows Update](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) ke stažení a instalaci aktualizací Windows. K dispozici jsou určitá nastavení, která používá klient web Windows Update při připojování k Windows Server Update Services (WSUS) nebo web Windows Update. Mnohé z těchto nastavení se dají spravovat pomocí:

- Editor místních zásad skupiny
- Zásady skupiny
- PowerShell
- Přímá úprava registru

Update Management respektuje mnoho nastavení určených k řízení web Windows Update klienta. Pokud použijete nastavení k povolení aktualizací jiných než Windows, Update Management tyto aktualizace taky spravovat. Pokud chcete povolit stahování aktualizací před tím, než dojde k nasazení aktualizace, může být nasazení aktualizace rychlejší, efektivnější a méně pravděpodobně větší než časový interval pro správu a údržbu.

## <a name="pre-download-updates"></a>Předběžné stažení aktualizací

Pokud chcete konfigurovat automatické stahování aktualizací, ale neinstalovat je automaticky, můžete pomocí Zásady skupiny nastavit [nastavení konfigurace automatických aktualizací](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. Toto nastavení umožňuje stáhnout požadované aktualizace na pozadí a upozorní vás, že jsou aktualizace připravené k instalaci. Tímto způsobem Update Management zůstane v řízení plánů, ale aktualizace se dají stáhnout mimo Update Management časový interval pro správu a údržbu. Toto chování brání časovému **intervalu údržby** při Update Management.

Nastavení tohoto nastavení můžete povolit pomocí prostředí PowerShell spuštěním následujícího příkazu:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Konfigurace nastavení restartování

Klíče registru uvedené v [části Konfigurace automatických aktualizací úpravou registru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) a [klíčů registru, které slouží ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , můžou způsobit restartování počítačů i v případě, že v nastavení **nasazení aktualizace** neurčíte **nikdy restartování** . Nakonfigurujte tyto klíče registru tak, aby nejlépe vyhovovaly vašemu prostředí.

## <a name="enable-updates-for-other-microsoft-products"></a>Povolit aktualizace pro ostatní produkty Microsoftu

Ve výchozím nastavení je web Windows Update klient nakonfigurován tak, aby poskytoval aktualizace pouze pro systém Windows. Pokud **při aktualizaci nastavení systému Windows povolíte aktualizace pro další produkty společnosti Microsoft** , obdržíte také aktualizace pro další produkty, včetně oprav zabezpečení pro Microsoft SQL Server a další software společnosti Microsoft. Tuto možnost lze nakonfigurovat, pokud jste stáhli a zkopírovali nejnovější [soubory šablon pro správu](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) , které jsou k dispozici pro systém Windows 2016 a novější.

Pokud používáte systém Windows Server 2012 R2, nelze toto nastavení konfigurovat pomocí Zásady skupiny. Na těchto počítačích spusťte následující příkaz PowerShellu. Update Management v souladu s tímto nastavením.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Nastavení konfigurace služby WSUS

Update Management podporuje nastavení služby WSUS. Nastavení služby WSUS, která můžete nakonfigurovat pro práci s Update Management, jsou uvedená níže.

### <a name="intranet-microsoft-update-service-location"></a>Umístění intranetové služby Microsoft Update

Můžete určit zdroje pro vyhledávání a stahování aktualizací v části [Zadejte umístění intranetové Microsoft Update služby](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Ve výchozím nastavení je web Windows Update klient nakonfigurován tak, aby stahoval aktualizace z web Windows Update. Pokud zadáte server WSUS jako zdroj pro počítače, pokud aktualizace nejsou schváleny ve službě WSUS, nasazení aktualizace se nepovede. 

Pokud chcete omezit počítače jenom na tuto interní službu aktualizace, nakonfigurujte [Nepřipojujte se k žádným web Windows Update internetovým umístěním](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Další kroky

Po konfiguraci nastavení web Windows Update můžete naplánovat nasazení aktualizací podle pokynů v tématu [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
