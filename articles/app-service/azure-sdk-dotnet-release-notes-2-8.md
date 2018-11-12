---
title: Zpráva k vydání verze Azure SDK pro .NET 2.8
description: Zpráva k vydání verze Azure SDK pro .NET 2.8
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
ms.openlocfilehash: 6aa2684a900dffecd481d51b8876b0e674c1a6ea
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235526"
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Sada Azure SDK for .NET 2.8, 2.8.1 a ve verzi 2.8.2
## <a name="overview"></a>Přehled
Tento článek obsahuje poznámky k verzi (které zahrnuje známé problémy a změny způsobující chyby) pro sadu Azure SDK pro .NET 2.8 2.8.1 a ve verzi 2.8.2 uvolní. 

Úplný seznam nových funkcí a aktualizace provedené v této verzi najdete v článku [Azure SDK 2.8 pro Visual Studio 2013 a Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) oznámení. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK pro .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Stáhnout sadu Azure SDK for .NET 2.8
[Sada Azure SDK for .NET 2.8 pro Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=699285) 

[Sada Azure SDK for .NET 2.8 pro Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Podpora .NET 4.5.2
#### <a name="known-issues"></a>Známé problémy
Azure SDK .NET 2.8 umožňuje vytvářet .NET 4.5.2 balíčky Cloudovou službu. Ale .NET 4.5.2 framework nenainstalují se výchozí verze hostovaného operačního systému obrázky až do ledna 2016 hostovaného operačního systému. Před 4.5.2, že, .NET framework bude k dispozici prostřednictvím samostatného vydané verze hostovaného operačního systému – listopad 2015-02. Najdete v článku [verzí hostovaného operačního systému Azure a sady SDK matice kompatibility](../cloud-services/cloud-services-guestos-update-matrix.md) stránky můžete sledovat, kdy budou vydané image.  Jakmile se uvolní obrazu listopadu 2015-02 můžete použít tuto image stačí aktualizovat vaše Cloudová služba konfigurační soubor (.cscfg) soubor. V konfiguraci služby souboru, nastavte atribut osVersion ServiceConfiguration element řetězec "WA-GUEST-operačního systému – 4.26_201511-02". Pokud budete chtít přihlásit k použití této image se už zobrazí automatické aktualizace do hostovaného operačního systému. Pro získávání automatických aktualizací verzi operačního systému musí být nastavena na "*" a .NET 4.5.2 budou k dispozici pouze prostřednictvím služby Automatické aktualizace v lednu 2016.

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>Známé problémy
Během **šablona objekt pro vytváření dat** projektu vytváření zahrnující ukázková data, skriptu azure power shell může selhat, pokud je nainstalovaná na počítači verze prostředí azure power po 0.9.8.

Chcete-li úspěšně vytvořit tento typ projektu, je nutné nainstalovat [azure power shell verzi 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Nástroje Azure Resource Manageru
#### <a name="breaking-changes"></a>Změny způsobující chyby
Aktualizovali jsme skript prostředí PowerShell v projektu skupiny prostředků Azure k dispozici v této verzi pro práci s nové rutiny prostředí Azure PowerShell verze 1.0.  Tento nový skript nebude fungovat z pomocí sady Visual Studio při použití na verzi sady SDK před 2.8.  

Skripty z projektů vytvořených v dřívějších verzích sady SDK se nespustí z Visual Studia při použití sady SDK 2.8.  Všechny skripty budou fungovat mimo sadu Visual Studio s odpovídající verzí rutin Azure Powershellu.  

2.8 SDK vyžaduje použití rutin prostředí Azure PowerShell verze 1.0.  Všechny verze sady SDK vyžadovat verzi 0.9.8 rutin Azure Powershellu.  Další informace najdete v části [to](https://go.microsoft.com/fwlink/?LinkID=623011) blogu.

### <a name="web-tools-extensions"></a>Webové nástroje – rozšíření
#### <a name="known-issues"></a>Známé problémy
Následující známé problémy bude vyřešen v následující verzi.

* App Service týkající se cloudu a Průzkumník serveru gesta pro neprodukčních prostředích (např. Azure China nebo Azure Stack zákazníků), nebudou fungovat. Pro zákazníky v těchto oblastech ovlivněné stahování profilu publikování na webu Azure Portal umožní možnost publikování. Budoucí verze bude opravit gesta, jako například "Připojit ladicí program" a "Zobrazit datový proud protokolů" pro Azure China a zákazníky zásobníku. 
* Zákazníci mohou zobrazí chyby během App Service se vytváří při instanci App Insights, do které provádíte nasazení se v oblasti, než USA – východ. V těchto případech umožní vytvoření služby App Service na portálu a stáhnout profil publikování scénáře publikování. 

### <a name="azure-hdinsight-tools"></a>Nástroje Azure HDInsight
#### <a name="new-updates"></a>Nové aktualizace
* Můžete spustit dotaz Hive v clusteru prostřednictvím HiveServer2 téměř bez režijních nákladů a najdete v protokolech úlohy v reálném čase.
* Pomocí nové Hive zobrazení spouštění úlohy při prozkoumávání lepší úlohy, najdete další podrobnosti a identifikovat případné problémy.

Informace najdete v tématu [Azure SDK 2.8 pro Visual Studio 2013 a Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK pro .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Známé problémy pro Visual Studio 2013 a Visual Studio 2015
1. Aktivované webové úlohy publikuje do sloty se zobrazit a chyba a nebude sada plánu, ale zařadí webové úlohy do Azure. Zákazníci, kteří jsou některé naplánované úlohy pak můžete na portálu Azure Pokud chcete nastavit plán pro webové úlohy. 
2. Python zákazníkům může docházet k problémům ladicího programu. Týmu služby zavádí opravu pro to, ale pokud zákazníků ovlivňuje, prosím dejte Microsoftu vědět, ve fórech nebo na blog s oznámením nebo oddílu pro komentáře poznámky k verzi. 
3. Zákazníci v určitých oblastech (jako je například Indie – jih) zaznamenají chyby zřizování služby App Service. To je konzistentní s portálem a Zákazníci, kteří tomuto problému dojde, můžete požádat o přístup k publikování na tyto geografické oblasti pomocí webu Azure portal. Jakmile požádá o přístup k těmto oblastem pomocí Azure portal zřizování by měl fungovat. 

## <a name="azure-sdk-for-net-282"></a>Azure SDK pro .NET ve verzi 2.8.2
Po instalaci ve verzi 2.8.2 nástroje, Zákazníci může docházet k následujícím problémům.         

* Pokud používáte Windows 10 a nenainstalovali jste aplikaci Internet Explorer, může zobrazit chyba "Aplikace Internet Explorer nebyl nalezen".
  Chcete-li vyřešit tento problém, nainstalujte Internet Explorer v dialogovém okně Přidat nebo odebrat součásti Windows.

Pokud můžete sledovat potíže, nahlaste ji pomocí funkce Odeslat smajlíka.

Další informace najdete v tématu [to](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) příspěvku.

## <a name="other-updates"></a>Další aktualizace
Další aktualizace, naleznete v tématu [Azure SDK 2.8 oznámení příspěvku](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Viz také
[Azure SDK 2.8 oznámení příspěvku](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Podpora a informace o vyřazení sady Azure SDK pro .NET a rozhraní API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

