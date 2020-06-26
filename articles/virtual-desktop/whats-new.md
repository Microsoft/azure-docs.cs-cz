---
title: Co je nového ve virtuálním počítači s Windows? – Azure
description: Nové funkce a aktualizace produktů pro virtuální počítač s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 7052e9203532320a7de5197e983d40cb0a34b50d
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374776"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Co je nového ve virtuálním počítači s Windows?

Virtuální počítače s Windows se pravidelně aktualizují. V tomto článku najdete informace o:

- Nejnovější aktualizace
- Nové funkce
- Vylepšení existujících funkcí
- Opravy chyb

Tento článek se aktualizuje měsíčně. Nezapomeňte se podívat, jak často se budou zobrazovat nové aktualizace.

## <a name="june-2020"></a>Červen 2020

Minulý měsíc představili jsme ve verzi Preview Standard Windows Virtual Desktop na jaře 2020 Update. Tato aktualizace má spoustu zajímavých nových funkcí, o kterých vám řekneme. Tady je co je novinka pro 2020 aktualizace.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>Virtuální počítač s Windows je teď integrovaný do Azure Resource Manager (Preview).

Virtuální počítač s Windows je teď integrovaný do Azure Resource Manager. V nejnovější aktualizaci jsou nyní všechny objekty virtuálních klientů Windows Azure Resource Manager prostředky. Tato aktualizace je také integrovaná s řízení přístupu na základě role (RBAC) v Azure. Další informace najdete v tématu [co je Azure Resource Manager?](../azure-resource-manager/management/overview.md) .

Tady je tato změna pro vás:

- Virtuální plocha Windows je teď integrovaná s Azure Portal. To znamená, že můžete všechno spravovat přímo na portálu, ale nevyžadují se žádné PowerShelly, webové aplikace ani nástroje třetích stran. Pokud chcete začít, podívejte se na náš kurz v [části Vytvoření fondu hostitelů pomocí Azure Portal](create-host-pools-azure-marketplace.md).

- Před aktualizací 2020 můžete publikovat jenom vzdálené aplikace RemoteApp a plochy pro jednotlivé uživatele. Pomocí Azure Resource Manager nyní můžete publikovat prostředky do skupin Azure Active Directory.

- Starší verze virtuálního počítače s Windows obsahovaly čtyři předdefinované role správce, které můžete přiřadit k tenantovi nebo fondu hostitelů. Tyto role jsou teď v [řízení přístupu na základě role](../role-based-access-control/overview.md)v Azure. Tyto role můžete použít pro každý objekt Azure Resource Manager virtuální plochy Windows, který vám umožní plně funkční model delegování.

- V případě aktualizace na jaře 2020 už nemusíte spouštět Azure Marketplace nebo šablonu GitHubu opakovaně, abyste mohli rozšířit fond hostitelů. K rozšíření fondu hostitelů stačí přejít do fondu hostitelů v Azure Portal a vybrat **+ Přidat** pro nasazení dalších hostitelů relací.

- Nasazení fondu hostitelů je teď plně integrované s [galerií sdílených imagí Azure](../virtual-machines/windows/shared-image-galleries.md). Galerie sdílených imagí je samostatná služba Azure, která ukládá definice imagí virtuálních počítačů (VM), včetně správy verzí imagí. K kopírování a posílání imagí do jiných oblastí Azure pro místní nasazení můžete použít taky globální replikaci.

- Funkce monitorování, které se použily k provedení prostřednictvím PowerShellu nebo webové aplikace diagnostické služby, se teď přesunuly na Log Analytics v Azure Portal. Nyní máte dvě možnosti vizualizace vašich sestav. Můžete spouštět dotazy Kusto a používat sešity k vytváření vizuálních sestav.

- Už nebudete muset Azure Active Directory (Azure AD) vyjádřit souhlas s používáním virtuálního klienta Windows. V aktualizaci jarní 2020 aktualizace tenant Azure AD v předplatném Azure ověřuje vaše uživatele a poskytuje pro správce ovládací prvky RBAC.


### <a name="powershell-support"></a>Podpora prostředí PowerShell

Do Azure PowerShell AZ Module byly přidány nové rutiny AzWvd s aktualizací jarní 2020. Tento nový modul se podporuje v PowerShell Core, který běží na .NET Core.

Pokud chcete modul nainstalovat, postupujte podle pokynů v části [nastavení modulu PowerShell pro virtuální počítač s Windows](powershell-module.md).

Seznam dostupných příkazů můžete zobrazit také v [Referenční příručce k AzWvd prostředí PowerShell](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Další informace o nových funkcích najdete v [našem blogovém příspěvku](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="additional-gateways"></a>Další brány

Přidali jsme nový cluster brány v Jižní Africe, aby se snížila latence připojení.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams na virtuálním počítači s Windows (Preview)

Provedli jsme několik vylepšení Microsoft Teams pro virtuální počítače s Windows. Nejdůležitější je, že Windows Virtual Desktop teď podporuje zvukové a vizuální přesměrování pro volání. Přesměrování vylepšuje latenci vytvořením přímých cest mezi uživateli při volání pomocí zvuku nebo videa. Menší vzdálenost znamená menší počet segmentů směrování, což způsobí, že volání budou vypadat a hladce zvuk.

Další informace najdete v [našem blogovém příspěvku](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Další kroky

Přečtěte si o budoucích plánech v [Microsoft 365 plán virtuálních klientů pro Windows](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).

V těchto článcích najdete informace o aktualizacích pro naše klienty pro virtuální počítače a službu Vzdálená plocha systému Windows:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
