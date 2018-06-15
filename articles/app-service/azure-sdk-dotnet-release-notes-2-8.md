---
title: Poznámky k verzi sady Azure SDK pro .NET 2.8
description: Poznámky k verzi sady Azure SDK pro .NET 2.8
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: de7207ff-ba4f-4008-9141-8742fcaa3254
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 0b9f55d69c824e86245738a082f95fc529583f58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "23836389"
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK pro .NET 2.8, 2.8.1 a 2.8.2
## <a name="overview"></a>Přehled
Tento článek obsahuje poznámky k verzi (které zahrnuje známé problémy a nejnovější změny) pro sadu Azure SDK pro .NET 2.8, 2.8.1 a 2.8.2 uvolní. 

Úplný seznam nových funkcí a aktualizace provedené v této verzi najdete v tématu [2.8 Azure SDK pro Visual Studio 2013 a Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) oznámení. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK pro .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Stáhněte si sadu Azure SDK pro .NET 2.8
[Azure SDK pro .NET 2.8 pro Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK pro .NET 2.8 pro Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Podpora rozhraní .NET 4.5.2
#### <a name="known-issues"></a>Známé problémy
Azure .NET SDK 2.8 vám umožní vytvořit .NET 4.5.2 balíčky cloudové služby. Ale 4.5.2 rozhraní .NET framework nebude nainstalován do výchozí verzi bitové kopie operačního systému hosta dokud leden 2016 hostovaného operačního systému. Před této, 4.5.2 rozhraní .NET framework bude k dispozici prostřednictvím samostatné verze operačního systému hosta verze – listopad 2015-02. Najdete v článku [verzí hostovaného operačního systému Azure a kompatibilních sad SDK](../cloud-services/cloud-services-guestos-update-matrix.md) stránky můžete sledovat, kdy budou vydané bitovou kopii.  Po vydání bitovou kopii. listopadu 2015-02 můžete použít tuto bitovou kopii při aktualizaci souboru (.cscfg) konfiguračním souboru cloudové služby. V konfiguraci služby souboru, nastavte atribut osVersion elementu objekt ServiceConfiguration řetězec "WA-GUEST-operačního systému-4.26_201511-02". Pokud zvolíte možnost vyjádřit výslovný souhlas k použití této image se už získat automatické aktualizace do hostovaného operačního systému. Chcete-li získat automatické aktualizace osVersion musí být nastavena na "*" a .NET 4.5.2 bude k dispozici pouze prostřednictvím funkce Automatické aktualizace v lednu 2016.

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>Známé problémy
Během **šablony objekt pro vytváření dat** projektu vytvoření zahrnující ukázková data, skriptu azure power shell může selhat, pokud je verze azure power shell v počítači nainstalován po 0.9.8.

Aby bylo možné úspěšně vytvořit tento typ projektu, je nutné nainstalovat [azure power shell verzi 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Nástroje Azure Resource Manager
#### <a name="breaking-changes"></a>Nejnovější změny
V této verzi fungovat s novými rutinami pro Azure PowerShell verze 1.0 byl aktualizován skript prostředí PowerShell poskytované projekt skupiny prostředků Azure.  Tento nový skript nebude fungovat z pomocí sady Visual Studio, pokud používáte verzi sady SDK před 2.8.  

Skripty z projektů vytvořených v dřívějších verzích sady SDK se nespustí z Visual Studia při použití 2,8 SDK.  Všechny skripty budou nadále fungovat mimo Visual Studio s příslušnou verzi rutin prostředí Azure PowerShell.  

2,8 SDK vyžaduje verzi 1.0 rutin prostředí Azure PowerShell.  Všechny verze sady SDK vyžadovat verzi 0.9.8 rutin prostředí Azure PowerShell.  Další informace najdete v části [to](http://go.microsoft.com/fwlink/?LinkID=623011) blogu.

### <a name="web-tools-extensions"></a>Rozšíření nástrojů Web
#### <a name="known-issues"></a>Známé problémy
Následující známé problémy budou řešeny v následující verzi.

* Služby App Service související cloudu a Průzkumníka serveru gesto pro nevýrobní prostředí (například Azure China nebo zákazníků Azure zásobníku) nebudou fungovat. Pro zákazníky v těchto oblastech ovlivněné stažení profilu publikování z portálu Azure povolíte možnost publikování. Budoucí verze bude opravit gesta například "Připojit ladicí program" a "Zobrazit streamování protokoly" pro Azure China a zásobníku zákazníků. 
* Zákazníci mohou zobrazit chyby během služby App Service je vytvoření při App Insights instance do které provádíte nasazení v jiné oblasti, než východní USA. V těchto scénářích se vytváří aplikační službu na portálu a stažení profilu publikování povolit publikování scénáře. 

### <a name="azure-hdinsight-tools"></a>Nástroje Azure HDInsight
#### <a name="new-updates"></a>Nové aktualizace
* Můžete provést dotaz Hive v clusteru prostřednictvím HiveServer2 s téměř bez režie a zjistit, že úloha protokolů reálném čase.
* Pomocí nové Hive zobrazení spouštění úlohy můžete proniknout do hlubší úlohu, najdete další podrobnosti a identifikovat potenciální problémy.

Informace najdete v tématu [2.8 Azure SDK pro Visual Studio 2013 a Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK pro .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Známé problémy pro Visual Studio 2013 a Visual Studio 2015
1. Publikuje aktivované webové úlohy do sloty bude zobrazit a chyba a nebude sada plánu, ale předá vytvářené webové úlohy do Azure. Zákazníci, kteří potřebují naplánovaná úloha pak můžete na portálu Azure k nastavení plánu pro vytvářené webové úlohy. 
2. Python zákazníků může zaznamenat problémy s ladicí program. Tým služby se zavedením opravu pro tento, ale pokud zákazníci jsou ovlivněni, sdělte Microsoft vědět ve fórech nebo na blogu oznámení nebo sekci komentáře poznámky k verzi. 
3. Zákazníci v určité oblasti (například – Jih, Indie) bude mít chyby zřizování App Service. To je konzistentní s portálem a zákazníky, kteří tomuto problému dojde, můžete použít portál Azure s požadavkem o přístup k publikování do těchto geografických oblastech. Jakmile budou požádat o přístup do těchto oblastí pomocí Azure portálu zřizování by měly fungovat. 

## <a name="azure-sdk-for-net-282"></a>Azure SDK pro .NET ve verzi 2.8.2
Po instalaci ve verzi 2.8.2 nástroje, zákazníků může zaznamenat následující problémy.         

* Pokud používáte Windows 10 a nemáte nainstalovanou aplikaci Internet Explorer, může dojít k chybě "Internet Explorer nebyla nalezena".
  Chcete-li vyřešit tento problém, nainstalujte aplikaci Internet Explorer pomocí dialogu přidat nebo odebrat součásti systému Windows.

Pokud zjistíte tento problém, použijte funkci odeslání smajlíka zprávu vytvoříte.

Další informace najdete v tématu [to](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) post.

## <a name="other-updates"></a>Další aktualizace
Další aktualizace, najdete v části [post oznámení Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Viz také
[Azure SDK 2.8 oznámení post](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Podporu a informace o vyřazení pro sadu Azure SDK pro .NET a rozhraní API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

