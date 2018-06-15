---
title: Při používání služby a vytváření aplikací pochopit klíčové rozdíly mezi Azure a Azure zásobníku | Microsoft Docs
description: Co potřebujete vědět o použití služeb nebo vývoj aplikací pro Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/16/2018
ms.author: mabrigg
ms.openlocfilehash: eab208175f7eb3b761ec7266483a7cd5268198e8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31512924"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Klíčové aspekty: pomocí služby nebo vytváření aplikací pro Azure zásobníku

Před použitím služby nebo vývoj aplikací pro Azure zásobníku, musíte pochopit rozdíly mezi zásobník Azure a Azure. Tento článek identifikuje klíčové faktory při použití Azure zásobníku jako vývojové prostředí hybridního cloudu.

## <a name="overview"></a>Přehled

Azure Stack je hybridní cloudová platforma, která umožňuje využívat služby Azure z datového centra vaší společnosti nebo vašeho poskytovatele služeb. Můžete sestavit aplikace v zásobníku Azure a pak je nasadit do protokolů Azure, Azure nebo Azure hybridní cloud.

Operátor vaší zásobník Azure umožňují vědět, které služby jsou k dispozici pro použití a jak získat podporu. Nabízejí tyto služby prostřednictvím jejich vlastní plány a nabídky.

Azure technického obsahu předpokládá, že jsou aplikací vyvíjených pro služby Azure místo zásobník Azure. Při vytvoření a nasazení aplikací do zásobníku Azure, musíte pochopit některé hlavní rozdíly, jako například:

* Zásobník Azure nabízí podmnožinu služby a funkce, které jsou k dispozici v Azure.
* Poskytovatel společnosti nebo služby můžete zvolit služby, které chtějí nabízejí. Dostupné možnosti mohou zahrnovat vlastní služby nebo aplikace. Nabízejí může vlastní vlastní dokumentace.
* Je nutné použít správné koncové body Azure specifické zásobníku (např. adresy URL adresu portálu a koncový bod Azure Resource Manager).
* Je nutné použít prostředí PowerShell a rozhraní API verze, které podporuje Azure zásobníku. Použití podporovaných verzí zajišťuje, že vaše aplikace bude fungovat v zásobníku Azure a Azure.

## <a name="cheat-sheet-high-level-differences"></a>Tahák: nejvýraznějších rozdílů

Následující tabulka popisuje nejvýraznějších rozdílů mezi zásobník Azure a Azure. Ponechat tyto rozdíly v úvahu při vývoji pro Azure zásobníku nebo použití služby Azure zásobníku.
*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

| Oblast | Azure (globální) | Azure Stack |
| -------- | ------------- | ----------|
| Kdo funguje? | Microsoft | Vaše organizace nebo služba Zprostředkovatel.|
| Kdo můžete požádat o podporu? | Microsoft | Integrovaný systém kontaktujte operátor Azure zásobníku (u vaší organizace nebo služba poskytovatele) pro podporu.<br><br>Pro podporu Azure zásobníku Development Kit, přejděte [fóra Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Development kit je zkušební prostředí, a proto není žádná oficiální podporu nabízených prostřednictvím Microsoft podporu služby zákazníkům (CSS).
| Služby k dispozici | Zobrazit seznam [Azure produkty](https://azure.microsoft.com/services/?b=17.04b). K dispozici služby se liší podle oblasti Azure. | Azure zásobníku podporuje podmnožinu služeb Azure. Skutečné služby budou lišit v závislosti na vaší organizace nebo služba Zprostředkovatel rozhodne na nabídku.
| Azure Resource Manager. koncový bod * | https://management.azure.com | Pro systém Azure zásobníku integrované pomocí koncového bodu, který poskytuje vaší operátor zásobník Azure.<br><br>Pro development kit použijte: https://management.local.azurestack.external
| Portál URL * | [https://portal.azure.com](https://portal.azure.com) | Pro systém Azure zásobníku integrované přejděte na adresu URL, kterou vaše operátor zásobník Azure poskytuje.<br><br>Pro development kit použijte: https://portal.local.azurestack.external
| Oblast | Můžete vybrat oblasti, které chcete nasadit. | Pro systém Azure zásobníku integrované použijte oblast, která je k dispozici v systému.<br><br>Pro development kit oblasti bude vždy **místní**.
| Skupiny prostředků | Skupiny prostředků může mít rozsah oblasti. | Pro integrované systémy a sadě pro vývoj existuje pouze jedna oblast.
|Podporované obory názvů, typy prostředků a verze rozhraní API | Nejnovější (nebo starší verze, které ještě nejsou zastaralé). | Azure zásobníku podporuje konkrétní verze. Najdete v části "Požadavky na verzi" v tomto článku.
| | |

* Pokud se operátor zásobník Azure, najdete v části [pomocí portálu správce](../azure-stack-manage-portals.md) a [základy správy](../azure-stack-manage-basics.md) Další informace.

## <a name="helpful-tools-and-best-practices"></a>Užitečné nástroje a doporučené postupy
 
 Společnost Microsoft poskytuje několik nástrojů a pokyny, které vám pomůže vývoji pro Azure zásobníku.

| Doporučení | Odkazy | 
| -------- | ------------- | 
| Nainstalujte nástroje pro správné na pracovní stanici developer. | - [Instalace prostředí PowerShell](azure-stack-powershell-install.md)<br>- [Stažení nástroje](azure-stack-powershell-download.md)<br>- [Konfigurace prostředí PowerShell](azure-stack-powershell-configure-user.md)<br>- [Instalaci sady Visual Studio](azure-stack-install-visual-studio.md) 
| Projděte si informace o následující položky:<br>-Aspekty šablon azure Resource Manager<br>– Postup nalezení šablony rychlý start<br>-Použijte modul zásad můžete použít Azure k vývoji pro Azure zásobníku | [Vývoj pro Azure Stack](azure-stack-developer.md) | 
| Zkontrolujte a použijte osvědčené postupy pro šablony. | [Rychlý start šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Požadavky verze

Azure zásobníku podporuje konkrétní verze prostředí Azure PowerShell a rozhraní API služby Azure service. Pomůže zajistit, že vaše aplikace můžete nasadit do obou protokolů Azure a Azure podporované verze.

Chcete-li mít jistotu, že používáte správnou verzi prostředí Azure PowerShell, použijte [profily verze rozhraní API](azure-stack-version-profiles.md). Pokud chcete zjistit profil nejnovější verzi rozhraní API, který můžete použít, zjistěte sestavení Azure ve vrstvách. používáte. Tyto informace můžete získat od správce Azure zásobníku.

>[!NOTE]
 Pokud používáte Azure zásobníku Development Kit a budete mít přístup pro správu, najdete v části "Zjistit aktuální verzi" [spravovat aktualizace](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) k určení sestavení Azure zásobníku.

Pro ostatní rozhraní API spusťte následující příkaz prostředí PowerShell k vypsání obory názvů, typy prostředků a verze rozhraní API, které jsou podporovány v rámci vašeho předplatného Azure zásobníku. Poznámka: může být stále rozdíly na úrovni vlastnost. (Pro tento příkaz fungovat, musíte již mít [nainstalován](azure-stack-powershell-install.md) a [nakonfigurované](azure-stack-powershell-configure-user.md) prostředí PowerShell pro Azure zásobníku prostředí. Musíte také mít předplatné Azure zásobníku nabídku.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Příklad výstupu (zkrácený): ![příklad výstupu příkazu Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Další postup

Podrobnější informace o rozdílech na úrovni služby najdete v části:

* [Důležité informace pro virtuální počítače v Azure zásobníku](azure-stack-vm-considerations.md)
* [Důležité informace týkající se úložiště v Azure zásobníku](azure-stack-acs-differences.md)
* [Důležité informace týkající se sítě Azure zásobníku](azure-stack-network-differences.md)
