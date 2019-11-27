---
title: Řešení potíží s Modul runtime služby Azure Functions je nedosažitelné.
description: Přečtěte si, jak řešit potíže s neplatným účtem úložiště.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 212f10bd33479e5a9f7244d5b2090c0324f937c2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226762"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Řešení potíží s modulem runtime funkcí je nedosažitelný.


## <a name="error-text"></a>Text chyby
Tento dokument je určený k odstraňování potíží s následující chybou, když se zobrazí na portálu Functions.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Souhrn
K tomuto problému dochází, když Modul runtime služby Azure Functions nepůjde spustit. Nejběžnějším důvodem této chyby je, že aplikace Function App ztratí přístup k účtu úložiště. [Přečtěte si další informace o požadavcích na účet úložiště.](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Poradce při potížích
Projdeme si čtyři nejběžnější chybové případy, jak identifikovat a jak vyřešit jednotlivé případy.

1. Účet úložiště se odstranil.
1. Odstranila se nastavení aplikace účtu úložiště.
1. Přihlašovací údaje účtu úložiště nejsou platné.
1. Účet úložiště není dostupný.
1. Úplná kvóta spuštění plná

## <a name="storage-account-deleted"></a>Účet úložiště se odstranil.

Každá aplikace Function App vyžaduje, aby účet úložiště fungoval. V případě odstranění tohoto účtu nebude funkce fungovat.

### <a name="how-to-find-your-storage-account"></a>Jak najít účet úložiště

Začněte tím, že v nastavení aplikace vyhledáte název svého účtu úložiště. Buď `AzureWebJobsStorage`, nebo `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` bude obsahovat název vašeho účtu úložiště zabaleného v připojovacím řetězci. Další podrobnosti najdete [tady v odkazu nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage) .

Vyhledejte svůj účet úložiště v Azure Portal a podívejte se, jestli stále existuje. Pokud byla odstraněna, bude nutné znovu vytvořit účet úložiště a nahradit připojovací řetězce úložiště. Váš kód funkce je ztracen a bude nutné ho znovu nasadit.

## <a name="storage-account-application-settings-deleted"></a>Odstranila se nastavení aplikace účtu úložiště.

Pokud jste v předchozím kroku neměli připojovací řetězec účtu úložiště, pravděpodobně jste ho odstranili nebo přepsali. Odstranění nastavení aplikace se obvykle provádí při použití slotů nasazení nebo Azure Resource Manager skriptů k nastavení nastavení aplikace.

### <a name="required-application-settings"></a>Požadovaná nastavení aplikace

* Požadováno
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Vyžadováno pro funkce plánu spotřeby
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Tady si přečtěte o těchto nastaveních aplikace.](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Doprovodné materiály

* U některého z těchto nastavení nezaškrtávejte možnost nastavení slotu. Při výměně slotů nasazení bude funkce přerušena.
* Tato nastavení neměňte v rámci automatizovaných nasazení.
* Tato nastavení musí být k dispozici a platná v době vytvoření. Automatizované nasazení, které neobsahuje tato nastavení, bude mít za následek nefunkční aplikaci, a to i v případě, že se nastavení přidá za fakt.

## <a name="storage-account-credentials-invalid"></a>Přihlašovací údaje účtu úložiště nejsou platné.

Pokud znovu vygenerujete klíče úložiště, je potřeba aktualizovat připojovací řetězce výše uvedeného účtu úložiště. [Přečtěte si další informace o správě klíčů úložiště tady.](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Účet úložiště není dostupný.

Vaše Function App musí být schopné získat přístup k účtu úložiště. Mezi běžné problémy, které blokují přístup funkcí k účtu úložiště, patří:

* Aplikace Function App nasazené do App Service prostředí bez správných síťových pravidel povolujících provoz do a z účtu úložiště
* Brána firewall účtu úložiště je povolená a není nakonfigurovaná tak, aby umožňovala provoz do a z funkcí. [Další informace o konfiguraci brány firewall pro účet úložiště najdete tady.](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Úplná kvóta spuštění plná

Pokud máte nakonfigurovanou denní kvótu spuštění, vaše Function App se dočasně zakáže a spousta ovládacích prvků portálu nebude k dispozici. 

* Chcete-li ověřit, zkontrolujte funkce Open Platform > Nastavení Function App na portálu. Pokud překročíte kvótu, zobrazí se následující zpráva.
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Pokud chcete tento problém vyřešit, odeberte kvótu a restartujte aplikaci.

## <a name="next-steps"></a>Další kroky

Teď, když je váš Function App zase v provozu, se podíváme na naše rychlé starty a vývojářské odkazy, abyste se mohli znovu začít pracovat!

* [Vytvoření první funkce Azure](functions-create-first-azure-function.md)  
  Umožňuje rovnou začít a vytvořit první funkci pomocí rychlého startu Azure Functions. 
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)  
  Poskytuje další odborné informace o modulu runtime Azure Functions a referenční informace pro kódování funkcí a definování triggerů a vazeb.
* [Testování Azure Functions](functions-test-a-function.md)  
  Toto téma popisuje různé nástroje a techniky pro testování funkcí.
* [Postup škálování Azure Functions](functions-scale.md)  
  Toto téma popisuje plány služby, které jsou dostupné se službou Azure Functions (včetně plánu hostování Consumption), a výběr správného plánu. 
* [Další informace o Azure App Service](../app-service/overview.md)  
  Azure Functions využívá službu Azure App Service pro základní funkce, jako jsou nasazení, proměnné prostředí a diagnostika. 
