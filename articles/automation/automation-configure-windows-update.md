---
title: Konfigurace nastavení služby Windows Update pro práci se správou aktualizací Azure
description: Tento článek popisuje nastavení služby Windows Update, která nakonfigurujete pro práci se správou aktualizací Azure.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279023"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Konfigurace nastavení služby Windows Update pro správu aktualizací

Azure Update Management spoléhá na [klienta Windows Update](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) ke stažení a instalaci aktualizací systému Windows. Existují specifická nastavení, která klient systému Windows Update používá při připojování ke službě WSUS (Windows Server Update Services) nebo Windows Update. Mnoho z těchto nastavení lze spravovat pomocí:

- Editor místních zásad skupiny
- Zásady skupiny
- PowerShell
- Přímo úprava registru

Správa aktualizací respektuje mnoho nastavení určených k řízení klienta služby Windows Update. Pokud používáte nastavení k povolení aktualizací, které nejsou součástí systému Windows, správa aktualizací bude také spravovat tyto aktualizace. Pokud chcete povolit stahování aktualizací před nasazením aktualizace, může být nasazení aktualizace rychlejší, efektivnější a méně pravděpodobné, že překročí okno údržby.

## <a name="pre-download-updates"></a>Aktualizace před stažením

Chcete-li nakonfigurovat automatické stahování aktualizací, ale neinstalovat je automaticky, můžete pomocí zásad skupiny nastavit [nastavení Konfigurovat automatické aktualizace](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) na **3**. Toto nastavení umožňuje stahování požadovaných aktualizací na pozadí a upozorní vás, že aktualizace jsou připraveny k instalaci. Tímto způsobem správa aktualizací zůstává pod kontrolou plánů, ale aktualizace lze stáhnout mimo okno údržby správy aktualizací. Toto chování zabraňuje **údržba okno překročeny** chyby ve správě aktualizací.

Toto nastavení můžete povolit pomocí prostředí PowerShell spuštěním následujícího příkazu:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Konfigurace nastavení restartování

Klíče registru uvedené v [části Konfigurace automatických aktualizací úpravou registru](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) a klíčů registru [používaných ke správě restartování](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mohou způsobit restartování počítačů, a to i v případě, že v nastavení nasazení aktualizace zadáte možnost Nikdy se **nerestartovat.** **Update Deployment** Nakonfigurujte tyto klíče registru tak, aby co nejlépe vyhovovaly vašemu prostředí.

## <a name="enable-updates-for-other-microsoft-products"></a>Povolení aktualizací pro jiné produkty společnosti Microsoft

Ve výchozím nastavení je klient služby Windows Update nakonfigurován tak, aby poskytoval aktualizace pouze pro systém Windows. Pokud při **aktualizaci** nastavení systému Windows povolíte funkci Poskytnout mi aktualizace pro jiné produkty společnosti Microsoft, obdržíte také aktualizace pro jiné produkty, včetně oprav zabezpečení pro server Microsoft SQL Server a další software společnosti Microsoft. Tuto možnost lze nakonfigurovat, pokud jste stáhli a zkopírovali nejnovější [soubory šablon pro správce,](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) které jsou k dispozici pro Windows 2016 a vyšší.

Pokud používáte systém Windows Server 2012 R2, nelze toto nastavení konfigurovat zásadami skupiny. Spusťte na těchto počítačích následující příkaz prostředí PowerShell. Správa aktualizací je v souladu s tímto nastavením.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Nastavení konfigurace služby WSUS

Správa aktualizací podporuje nastavení služby WSUS. Nastavení služby WSUS, která můžete nakonfigurovat pro práci se správou aktualizací, jsou uvedena níže.

### <a name="intranet-microsoft-update-service-location"></a>Umístění služby aktualizace sítě Intranet společnosti Microsoft

Zdroje pro skenování a stahování aktualizací můžete určit v části [Zadat umístění služby Microsoft Update v intranetu](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Ve výchozím nastavení je klient služby Windows Update nakonfigurován ke stahování aktualizací ze služby Windows Update. Pokud zadáte server WSUS jako zdroj pro vaše počítače, pokud aktualizace nejsou schváleny v systému WSUS, nasazení aktualizace se nezdaří. 

Chcete-li omezit pouze tuto interní aktualizační službu, nakonfigurujte [možnost Nepřipojovat se k žádným internetovým umístěním služby Windows Update](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Další kroky

Po konfiguraci nastavení služby Windows Update můžete naplánovat nasazení aktualizace podle pokynů v části [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
