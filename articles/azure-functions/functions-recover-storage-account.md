---
title: Řešení potíží se modul Runtime služby Azure Functions nedostupný.
description: Informace o řešení potíží s účet úložiště nejsou platné.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 1902091978233ecaf80f04e3a08c70c20aee42c9
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000015"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Řešení potíží s "modul runtime služby functions nedostupný"


## <a name="error-text"></a>Text chyby
Tento dokument je určený k odstranění následující chyby při zobrazení na portálu Functions.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Souhrn
Tento problém nastane, pokud nelze spustit modul Runtime služby Azure Functions. Nejčastější příčinou této chyby na výskyt je aplikace function app ztráty přístupu k jeho účet úložiště. [Další informace o požadavcích účtu úložiště tady](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Řešení potíží
Projdeme čtyři nejběžnější případy chyb, jak identifikovat a jak řešit každý případ.

1. Odstranit účet úložiště
1. Odstranit nastavení aplikace účtu úložiště
1. Neplatná pověření účtu úložiště
1. Účet úložiště je nedostupný
1. Denní kvóty spuštění úplné

## <a name="storage-account-deleted"></a>Odstranit účet úložiště

Všechny aplikace function app vyžaduje účet úložiště pro provoz. Pokud tento účet je odstraněný funkce nebudou fungovat.

### <a name="how-to-find-your-storage-account"></a>Vyhledání účtu úložiště

Začněte tím, že název účtu úložiště v nastavení aplikace hledání. Buď `AzureWebJobsStorage` nebo `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` bude obsahovat název vašeho účtu úložiště obalilo v připojovacím řetězci. Přečtěte si další podrobnosti na [nastavení odkaz aplikace](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Vyhledání účtu úložiště na webu Azure Portal Pokud stále existuje. Pokud byl odstraněn, bude muset znovu vytvořit účet úložiště a nahraďte připojovací řetězce úložiště. Kód vaší funkce je ztraceny a budete muset znovu nasadit znovu.

## <a name="storage-account-application-settings-deleted"></a>Odstranit nastavení aplikace účtu úložiště

V předchozím kroku by neměl připojovací řetězec účtu úložiště byly pravděpodobně odstraněny nebo přepsat. Odstranění nastavení aplikace se obvykle provádí při používání slotů nasazení nebo skriptů Azure Resource Manageru k nastavení aplikace.

### <a name="required-application-settings"></a>Požadovaná aplikace nastavení

* Požaduje se
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Vyžaduje se pro funkce plánu Consumption
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#websitecontentazurefileconnectionstring)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#websitecontentshare)

[Přečtěte si informace o nastavení aplikace](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Doprovodné materiály

* Nezaškrtávejte políčko "nastavení slotu" pro některé z těchto nastavení. Pokud jste prohození slotů nasazení funkce se přeruší.
* Nenastavujte tato nastavení při použití automatického nasazení.
* Tato nastavení musí být zadaná a je platný v okamžiku vytvoření. Automatického nasazení, který neobsahuje toto nastavení způsobí neovlivňující aplikace i v případě, že nastavení jsou přidány po jejich výskytu.

## <a name="storage-account-credentials-invalid"></a>Neplatná pověření účtu úložiště

Výše uvedené připojovací řetězec účtu úložiště musí být aktualizován, je-li znovu vygenerovat klíče úložiště. [Přečtěte si víc o službě storage zde Správa klíčů](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#manage-your-storage-account)

## <a name="storage-account-inaccessible"></a>Účet úložiště je nedostupný

Aplikace Function App musí mít pro přístup k účtu úložiště. Běžné problémy, tento blok funkcí přístup k účtu úložiště patří:

* Aplikace Function App nasadit do služby App Service Environment bez správné síťové pravidel pro povolení provozu do a z účtu úložiště
* Brána firewall účet úložiště je povolena a není nakonfigurována pro povolení provozu do a z funkce. [Další informace o účtu brány firewall na konfiguraci úložiště tady](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Denní kvóty spuštění úplné

Pokud máte denní kvóty spuštění nakonfigurovat aplikaci Function App se dočasně zakázané a řadu řízení portálu nebude k dispozici. 

* Pokud chcete ověřit, otevřete kontrola funkce platformy > Nastavení aplikace Function App na portálu. Tato zpráva se zobrazí, pokud jste překročili kvótu
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Odebrat kvótu a restartujte aplikaci k vyřešení daného problému.

## <a name="next-steps"></a>Další kroky

Teď, když vaše aplikace Function App se zpět a provozní podívejte se na našich šablon rychlý start a developer odkazy zprovoznit a spustit znovu.

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
