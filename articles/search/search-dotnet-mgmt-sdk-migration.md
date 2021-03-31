---
title: Upgrade na sadu Azure Search .NET Management SDK
titleSuffix: Azure Cognitive Search
description: Upgradujte na sadu Azure Search .NET Management SDK z předchozích verzí. Přečtěte si o nových funkcích a změnách kódu nezbytných pro migraci.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936703"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Upgrade verzí sady Azure Search .NET Management SDK

Tento článek vysvětluje, jak migrovat do po sobě jdoucí verze sady Azure Search .NET Management SDK, která se používá ke zřízení nebo zrušení zřízení služby Search Services, úpravě kapacity a správě klíčů rozhraní API.

Sady SDK pro správu cílí na konkrétní verzi REST API správy. Další informace o konceptech a operacích najdete v tématu [Správa hledání (REST)](/rest/api/searchmanagement/).

## <a name="versions"></a>Verze

| SDK version (Verze sady SDK) | Odpovídající verze REST API | Přidání nebo změna chování funkce |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | API-Version = 2020-30-20 | Přidá zabezpečení koncového bodu (brány firewall IP a integrace pomocí [privátního odkazu Azure](../private-link/private-endpoint-overview.md)). |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | API-Version = 2019-10-01 | Vylepšení použitelnosti. Zásadní změna [klíčů dotazu seznamu](/rest/api/searchmanagement/querykeys/listbysearchservice) (přestává se ukončit). |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | API-Version = 2015-08-19  | První verze |

## <a name="how-to-upgrade"></a>Postup upgradu

1. Aktualizujte svůj odkaz na NuGet pro `Microsoft.Azure.Management.Search` použití buď konzoly Správce balíčků NuGet, nebo kliknutím pravým tlačítkem na odkazy na projekt a výběrem možnosti spravovat balíčky NuGet... v aplikaci Visual Studio.

1. Jakmile NuGet stáhne nové balíčky a jejich závislosti, sestavte projekt znovu. V závislosti na tom, jak je váš kód strukturovaný, se může úspěšně znovu sestavit, v takovém případě jste hotovi.

1. Pokud sestavení selhalo, může to být způsobeno tím, že jste implementovali některá rozhraní sady SDK (například pro účely testování částí), které se změnily. Chcete-li tento problém vyřešit, budete muset implementovat novější metody, jako je například `BeginCreateOrUpdateWithHttpMessagesAsync` .

1. Po opravě chyb sestavení můžete v aplikaci provádět změny, abyste mohli využívat nové funkce. 

## <a name="upgrade-to-30"></a>Upgradovat na 3,0

Verze 3,0 přidává ochranu privátních koncových bodů tím, že omezuje přístup k rozsahům IP adres a volitelně je integruje s privátním propojením Azure pro služby vyhledávání, které by se neměly zobrazovat na veřejném Internetu.

### <a name="new-apis"></a>Nová rozhraní API

| Rozhraní API | Kategorie| Podrobnosti |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | Brána firewall protokolu IP | Omezte přístup ke koncovému bodu služby na seznam povolených IP adres. Koncepty a pokyny pro portál najdete v tématu [Konfigurace brány firewall protokolu IP](service-configure-firewall.md) . |
| [Prostředek sdíleného privátního propojení](/rest/api/searchmanagement/sharedprivatelinkresources) | Privátní propojení | Vytvořte sdílený prostředek privátního propojení, který bude používán vyhledávací službou.  |
| [Připojení privátního koncového bodu](/rest/api/searchmanagement/privateendpointconnections) | Privátní propojení | Navázání a Správa připojení k vyhledávací službě prostřednictvím privátního koncového bodu. Koncepty a pokyny pro portál najdete v tématu [Vytvoření privátního koncového bodu](service-create-private-endpoint.md) .|
| [Prostředky privátního propojení](/rest/api/searchmanagement/privatelinkresources/) | Privátní propojení | Pro vyhledávací službu, která má připojení privátního koncového bodu, získejte seznam všech služeb, které se používají ve stejné virtuální síti. Pokud vaše řešení pro vyhledávání obsahuje indexery, které jsou vyžádané ze zdrojů dat Azure (Azure Storage, Cosmos DB, Azure SQL), nebo používají Cognitive Services nebo Key Vault, musí mít všechny tyto prostředky ve virtuální síti koncové body a toto rozhraní API by mělo vracet seznam. |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Privátní propojení | Toto je vlastnost pro žádosti o vytvoření nebo aktualizaci služby. Když je tato zakázaná, privátní odkaz je jediným rozhraním přístupu. |

### <a name="breaking-changes"></a>Změny způsobující chyby

Nemůžete už použít možnost získat u žádosti o [klíč dotazu seznamu](/rest/api/searchmanagement/querykeys/listbysearchservice) . V předchozích verzích můžete použít buď GET nebo POST, v této verzi a ve všech verzích, které se přesunou nahoru, podporuje se jenom POST. 

## <a name="upgrade-to-20"></a>Upgradovat na 2,0

Verze 2 sady Azure Search .NET Management SDK je menší upgrade, takže změna kódu by měla vyžadovat jenom minimální úsilí. Změny v sadě SDK jsou čistě změny na straně klienta, aby se zlepšila použitelnost samotné sady SDK. Dochází k následujícím změnám:

* `Services.CreateOrUpdate` a jeho asynchronní verze nyní automaticky dotazují zřizování `SearchService` a nevrátí se do dokončení zřizování služby. Tím ušetříte, abyste si tento kód cyklického dotazování sami napsali sami.

* Pokud přesto chcete, aby se zřizování služby dotazoval ručně, můžete použít novou `Services.BeginCreateOrUpdate` metodu nebo jednu z jejích asynchronních verzí.

* Nové metody `Services.Update` a její asynchronní verze byly přidány do sady SDK. Tyto metody používají opravu HTTP k podpoře přírůstkové aktualizace služby. Můžete například nyní škálovat službu předáním `SearchService` instance těmto metodám, které obsahují pouze požadované `partitionCount` `replicaCount` vlastnosti a. Starý způsob volání `Services.Get` , úpravy vráceného `SearchService` a jeho předání na `Services.CreateOrUpdate` je stále podporován, ale již není nutný. 

## <a name="next-steps"></a>Další kroky

Pokud narazíte na problémy, je nejlepší fórum pro publikování dotazů [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Pokud narazíte na chybu, můžete založit problém v [úložišti GitHub Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Ujistěte se, že název problému bude označovat text "[Search]".