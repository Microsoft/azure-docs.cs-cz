---
title: Rozšíření pro Cloud Services (Rozšířená podpora)
description: Rozšíření pro Cloud Services (Rozšířená podpora)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6db43acda679c6c1c1edd6336f693cc4757b6d45
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220913"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Rozšíření pro Cloud Services (Rozšířená podpora)

Rozšíření jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace pro role. Můžete například povolit připojení ke vzdálené ploše v roli během nasazení cloudové služby (rozšířené podpory) pomocí rozšíření vzdálené plochy.  

## <a name="remote-desktop-extension"></a>Rozšíření vzdálené plochy

Vzdálená plocha umožňuje přístup k ploše role spuštěné v Azure. Připojení ke vzdálené ploše můžete použít k řešení potíží a diagnostikování problémů s aplikací v době, kdy je spuštěná.

Během vývoje můžete povolit připojení ke vzdálené ploše ve vaší roli, a to zahrnutím modulů vzdálené plochy do definice služby nebo prostřednictvím rozšíření vzdálené plochy. 

Další informace najdete v tématu [Konfigurace vzdálené plochy z Azure Portal](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Rozšíření pro Windows Azure Diagnostics

Můžete monitorovat klíčové metriky výkonu pro libovolnou cloudovou službu. Každá role cloudové služby shromažďuje minimální data: využití CPU, využití sítě a využití disku. Pokud má cloudová služba rozšíření Microsoft. Azure. Diagnostics použité pro roli, může tato role shromažďovat další body dat. 

Díky základnímu monitorování jsou data čítače výkonu z instancí rolí ve vzorku a shromažďována v intervalech po dobu 3 minut. Tato základní data monitorování se ve vašem účtu úložiště neukládají a nevztahují se na ně žádné další náklady. 

S pokročilým monitorováním jsou vzorky a shromážděny další metriky v intervalech 5 minut, 1 hodina a 12 hodin. Agregovaná data jsou uložená v účtu úložiště v tabulkách a vyprázdní se po 10 dnech. Použitý účet úložiště je nakonfigurovaný rolí. pro různé role můžete použít jiné účty úložiště. 

Další informace najdete v tématu [použití rozšíření Windows Azure Diagnostics v Cloud Services (Rozšířená podpora)](enable-wad.md) .

## <a name="anti-malware-extension"></a>Antimalwarové rozšíření
Aplikace nebo služba Azure může povolit a nakonfigurovat Microsoft Antimalware pro Azure Cloud Services pomocí rutin PowerShellu. Upozorňujeme, že Microsoft Antimalware je nainstalovaný v zakázaném stavu na Cloud Services platformě se systémem Windows Server 2012 R2 a starším, který vyžaduje, aby aplikace Azure tuto akci povolila. Pro Windows Server 2016 a novější je Windows Defender ve výchozím nastavení povolený, takže tyto rutiny se dají použít ke konfiguraci antimalwarového programu.

Další informace najdete v tématu [Přidání Microsoft antimalwaru do cloudové služby Azure pomocí rozšířené podpory (cs-ES)](https://docs.microsoft.com/azure/security/fundamentals/antimalware-code-samples#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support) .

Pokud chcete získat další informace o antimalwaru Azure, navštivte prosím [Toto](https://docs.microsoft.com/azure/security/fundamentals/antimalware) .



## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
