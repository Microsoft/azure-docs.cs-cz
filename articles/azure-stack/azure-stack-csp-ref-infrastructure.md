---
title: Využití sestav infrastruktury pro poskytovatele cloudových služeb pro Azure Stack | Dokumentace Microsoftu
description: Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití u klientů, které jsou obsluhovány pomocí Cloud Service Provider (CSP) nastane a předá ji do Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: 9526385eaea8a88f0c22e6420ba39a33f7166f96
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633720"
---
## <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Využití sestav infrastruktury pro poskytovatele cloudových služeb

Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití, protože dojde k a předá ji do Azure. V Azure Azure Commerce zpracovává data o využití a poplatky za využití pro příslušné předplatné Azure. V takovém stejným způsobem jako sledování využití se sleduje v globální cloud Azure.

Všimněte si, že jsou některé pojmy konzistentní mezi global Azure a Azure Stack. Azure Stack je místní odběry, které splňují podobnou roli s předplatným Azure. Místní odběry jsou platné pouze místně. Místní odběry se mapují na předplatná Azure, když využití je předán do Azure.

Azure Stack je místní využití měřičů. Místní využití je namapována na měřiče použít v Azure commerce. ID měřiče se však liší. K dispozici další měřiče místně než ten, který společnost Microsoft používá pro účely fakturace.

Existuje několik rozdílů mezi jaké jsou ceny za služby ve službě Azure Stack a Azure. Například ve službě Azure Stack, poplatky za virtuální počítače je jenom na základě hodiny jader vcore /, se stejná sazba pro všechny řady virtuálních počítačů, na rozdíl od Azure. Důvodem je, že globální Azure různé ceny odrážejí jiný hardware. Ve službě Azure Stack poskytuje zákazník hardware, proto neexistuje žádný důvod platby prostřednictvím různých účtovat sazby platné pro různé třídy virtuálního počítače.

Můžete získat informace o měřiče služby Azure Stack, který je používán obchodování a jejich cenu v partnerském centru stejným způsobem jako u služby Azure:

1. V partnerském centru, přejděte **nabídce řídicího panelu** > **cenami a nabídkami**.
2. V části **založená na využití služby**vyberte **aktuální**.
3. Otevřít **Azure v ceníku CSP globální** tabulky.
4. Filtrovat podle **oblasti = Azure Stack**.

## <a name="usage-and-billing-error-codes"></a>Využití a fakturace kódy chyb

Následujících chybových zpráv může nastat při přidání klienty na registraci.

| Chyba                           | Podrobnosti                                                                                                                                                                                                                                                                                                                           | Komentáře                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RegistrationNotFound            | Zadaný registrační nebyl nalezen. Ujistěte se, že byla správně zadat následující informace:<br>1. Identifikátor předplatného (poskytnutá hodnota: _identifikátor předplatného_),<br>2. Skupina prostředků (poskytnutá hodnota: _skupiny prostředků_),<br>3. Registrace – název (poskytnutá hodnota: _registrace – název_).                             | K této chybě obvykle dochází, když informace o počáteční registraci není správný. Pokud je potřeba ověřit skupinu prostředků a název svou registraci, najdete ho na webu Azure Portal, uvedením všechny prostředky. Pokud zjistíte více než jeden prostředek registrace, podívejte se na CloudDeploymentID ve vlastnostech a vyberte registraci, jejichž CloudDeploymentID odpovídá vašeho cloudu. Najít CloudDeploymentID, můžete použít toto prostředí PowerShell ve službě Azure Stack:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId       | Poskytnuté _identifikátor předplatného zákazníka_ a _registrace – název_ identifikátor předplatného nejsou vlastněny stejné poskytovatele cloudové služby Microsoftu. Zkontrolujte, zda je správný identifikátor předplatného zákazníka. Pokud se problém nevyřeší, obraťte se na podporu. | Tato chyba nastane, pokud zákaznické předplatné je předplatném CSP, ale jeho shrnuje CSP partner odlišné od těch, ke kterému shrnuje předplatného použité v počáteční registraci. Tato kontrola se provádí, aby se zabránilo situaci, která by za následek účtování partnerem CSP, který není zodpovědná za Azure Stack, používá.                                                                                                                                                                                                                                                                          |
| InvalidCustomerSubscriptionId   | "_Identifikátor předplatného zákazníka_' není platný. Ujistěte se, že je k dispozici platné předplatné Azure.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| CustomerSubscriptionNotFound    | _Identifikátor předplatného zákazníka_ pod názvem _registration nebyl nalezen. Ujistěte se, že se používá platné předplatné Azure a zda identifikátor předplatného byl přidán k registraci pomocí operace PUT.                                                   | K této chybě dochází při pokusu o závažnost neshody tenanta se přidala do předplatného, který nebyl nalezen předplatné zákazníka má být přidružena k registraci. Zákazník nebyla přidána k registraci nebo ID předplatného je napsán správně.                                                                                                                                                                                                                                                                                                                                |
| UnauthorizedCspRegistration     | Poskytnuté _registrace – název_ není schválený pro použití více tenantů. Odeslání e-mailu azstCSP@microsoft.com a zahrnují vaší registrace – název, skupinu prostředků a předplatném identifikátor použitý v registraci.                                                                                    | Registrace musí schválit pro více tenantů společností Microsoft před zahájením přidání tenantů do něj. Najdete v části registrace tenantům v tomto dokumentu další vysvětlení.                                                                                                                                                                                                                                                                                                                                                                                                             |
| CustomerSubscriptionsNotAllowed | Odpojené zákazníkům se nepodporují operace předplatných zákazníků. Aby bylo možné tuto funkci použít, znovu zaregistrovat platit jako můžete použít licencí.                                                                                                                                                                    | Registrace, ke kterému se pokoušíte přidat klienty je kapacita registrace, to znamená, při registraci byl vytvořen, parametr, který byl použit BillingModel kapacity. Platíte jenom při použití registrace jsou povoleny pro přidání tenantů. Budete muset znovu zaregistrovat pomocí parametru BillingModel PayAsYouUse.                                                                                                                                                                                                                                                                                          |
| InvalidCSPSubscription          | Poskytnuté _identifikátor předplatného zákazníka_ není platné předplatné poskytovatele CSP. Ujistěte se, že je k dispozici platné předplatné Azure.                                                                                                                                                        | Jde pravděpodobně z důvodu zákaznického předplatného se chybně zadaná.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| MetadataResolverBadGatewayError | Některý nadřazený server vrátil neočekávanou chybu. Zkuste to znovu později. Pokud se problém nevyřeší, obraťte se na podporu.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Termíny používané pro fakturaci a využití

Následující terminologie a Koncepty jsou použity pro využití a fakturace ve službě Azure Stack:

| Označení | Definice |
| --- | --- |
| Přímé partner CSP | Přímé partner Cloud Solution Provider (CSP) obdrží fakturu přímo od společnosti Microsoft pro Azure a Azure Stackem využití a faktur zákazníkům přímo. |
| Nepřímé CSP | Nepřímí prodejci pracovat nepřímý poskytovatel (označované také jako distributor). Získávání koncovým zákazníkům; je prodejcům v systému nepřímý poskytovatel má fakturační vztah se společností Microsoft, spravuje fakturace zákazníkům a poskytuje další služby, třeba odborné pomoci. |
| Koncového zákazníka | Koncovým zákazníkům se podniky a instituce státní správy, které vlastní aplikace a další úlohy, které běží ve službě Azure Stack. |

## <a name="next-steps"></a>Další postup

 - Další informace o programu CSP najdete v tématu [programu Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
 - Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](azure-stack-billing-and-chargeback.md).
