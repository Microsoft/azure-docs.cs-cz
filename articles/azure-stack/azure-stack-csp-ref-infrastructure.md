---
title: "Využití sestav infrastruktury pro poskytovatele cloudových služeb pro Azure zásobníku | Microsoft Docs"
description: "Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití tak, jak dojde k a předává do Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 4ac808e0e85b1daeb54a3f2fd7bec0a7c10aa13e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
## <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Využití sestav infrastruktury pro poskytovatele cloudových služeb

Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití tak, jak dojde k a předává do Azure. V Azure Azure a obchodu Spojených států zpracovává dat o využití a poplatky využití pro příslušné předplatné Azure, stejně jako využití, která se provádí v globální cloudu Azure.

Je třeba si uvědomit, že některé pojmy jsou konzistentní mezi globální Azure a Azure zásobníku. Sada Azure má místní odběry, které splňují podobnou roli k předplatnému Azure. Místní odběry jsou platné pouze místně. Místní odběry jsou namapované na předplatná Azure, při využití se předají do Azure.

Sada Azure má místní využití měřidla. Místní využití je namapována na měřidla používají v obchodu Spojených států v Azure. ID měření se ale liší. Nejsou k dispozici místně než ten, který společnost Microsoft používá pro fakturaci další měřidla.

Existují určité rozdíly mezi jak jsou služby za cenu v zásobníku Azure a Azure. Například v zásobníku Azure zdarma pro virtuální počítače se jenom na základě vcore/hodin, s stejné rychlost pro všechny řady virtuálních počítačů, na rozdíl od Azure. Důvodem je, že globální Azure různé ceny odrážet jiný hardware. V zásobníku Azure poskytuje zákazník hardware, proto neexistuje žádný důvod k účtují různé sazby pro různé třídy virtuálních počítačů.

Můžete zjistit o měřidla zásobník Azure používají v obchodu a jejich ceny v partnerské Centrum stejným způsobem jako u služby Azure:

1. V partnerské centrum, přejděte do **nabídce řídícího panelu** > **– ceny a nabízí**.
2. V části **služeb na základě využití**, vyberte **aktuální**.
3. Otevřete **Azure v ceníku, který globální CSP** tabulky.
4. Filtrovat **oblast = Azure Stack**.

## <a name="usage-and-billing-error-codes"></a>Využití a fakturace kódy chyb

Následující chybové zprávy můžete došlo při přidávání klienty na registraci.

| Chyba                           | Podrobnosti                                                                                                                                                                                                                                                                                                                           | Komentáře                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RegistrationNotFound            | Zadaný registrační nebyl nalezen. Zkontrolujte, zda že bylo správně zadáno následující informace:<br>1. Identifikátor předplatného (Zadaná hodnota: _identifikátor předplatného_),<br>2. Skupina prostředků (Zadaná hodnota: _skupiny prostředků_),<br>3. Název registrace (Zadaná hodnota: _název registrace_).                             | Této chybě obvykle dochází, když informace o počáteční registrace, není správný. Pokud je třeba ověřit skupinu prostředků a název registrace, můžete ji najít na portálu Azure tak, že uvedete všechny prostředky. Pokud zjistíte, více než jeden prostředek registrace, podívejte se na CloudDeploymentID ve vlastnostech a vyberte registrace, k jejichž CloudDeploymentID odpovídá ve vašem cloudu. Najít CloudDeploymentID, můžete tuto prostředí PowerShell v zásobníku Azure:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId       | Poskytnutého _identifikátor předplatného zákazníka_ a _název registrace_ identifikátor předplatného nejsou vlastněny stejné poskytovatele cloudové služby společnosti Microsoft. Zkontrolujte, zda je správný identifikátor předplatného zákazníka. Pokud potíže potrvají, obraťte se na podporu. | K této chybě dojde, když je předplatné zákazníka předplatného poskytovatele CSP, ale jeho shrnuje partnerovi CSP liší od verze, do které shrnuje předplatné použité v počáteční registraci. Tato kontrola se provádí, aby se zabránilo situaci, který by mělo za následek fakturace partnera CSP, který není zodpovědná za zásobník Azure používat.                                                                                                                                                                                                                                                                          |
| InvalidCustomerSubscriptionId   | '_Identifikátor předplatného zákazníka_' není platný. Zkontrolujte, zda že je k dispozici platné předplatné Azure.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| CustomerSubscriptionNotFound    | _Identifikátor předplatného zákazníka_ pod názvem _registration nebyl nalezen. Zajistěte, aby se používá platné předplatné Azure a že identifikátor odběru byl přidán k registraci pomocí operace PUT.                                                   | K této chybě dojde při pokusu o važnost neshody, který klienta byla přidána do předplatného, a předplatné zákazníka nebyl nalezen přidruženi k registraci. Zákazník nebyl přidán do registrace nebo ID odběru byla zapsána nesprávně.                                                                                                                                                                                                                                                                                                                                |
| UnauthorizedCspRegistration     | Poskytnutého _název registrace_ není schválený používat víceklientská architektura. E-mailovou zprávu na azstCSP@microsoft.com a obsahovat název vaší registrační, skupinu prostředků a identifikátor odběru, který je používán registrace.                                                                                    | Registrace je třeba Microsoft schválit víceklientský předtím, než můžete začít přidávat klienty k němu. Najdete v části registrace klientům v tomto dokumentu další vysvětlení.                                                                                                                                                                                                                                                                                                                                                                                                             |
| CustomerSubscriptionsNotAllowed | Operace předplatná zákazníka nejsou podporovány pro zákazníky, odpojené. Chcete-li použít tuto funkci, opakujte registraci s platit jako můžete použít licencování.                                                                                                                                                                    | Registrace, ke kterému se pokoušíte přidat klienty je registrace kapacitu, tedy registrace v okamžiku vytvoření parametr, který byl použit BillingModel kapacity. Platit pouze při používání registrace jsou povoleny pro přidání klientů. Budete muset znovu zaregistrovat pomocí parametru BillingModel PayAsYouUse.                                                                                                                                                                                                                                                                                          |
| InvalidCSPSubscription          | Poskytnutého _identifikátor předplatného zákazníka_ není platné předplatné CSP. Zkontrolujte, zda že je k dispozici platné předplatné Azure.                                                                                                                                                        | To je ve většině případů pravděpodobně z důvodu se chybně předplatné zákazníka.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| MetadataResolverBadGatewayError | Jeden z nadřazené servery vrátil neočekávanou chybu. Zkuste to znovu později. Pokud potíže potrvají, obraťte se na podporu.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Termíny používané pro fakturaci a využití

Následující podmínky a Koncepty jsou používány pro využití a fakturace v zásobníku Azure:

| Označení | Definice |
| --- | --- |
| Přímé partnera zprostředkovatele kryptografických služeb | Přímé partnera Cloud Solution Provider (CSP) obdrží faktury přímo přímo společnosti Microsoft pro Azure a Azure zásobníku využití a faktur zákazníků. |
| Nepřímý zprostředkovatele kryptografických služeb | Nepřímý prodejce pracovat poskytovatele nepřímých (také označované jako distributor). Obě nábor koncovým zákazníkům; nepřímý poskytovatele má fakturační vztah se společností Microsoft, spravuje zákazníka fakturace a poskytuje další služby, jako je podpora produktu. |
| Koncového zákazníka | Podniky a organizace státní správy, které vlastní aplikace a další úlohy, které běží na Azure zásobníku je end zákazníků. |

## <a name="next-steps"></a>Další postup

 - Další informace o programu CSP, najdete v části [programu poskytovatele cloudových řešení](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Další informace o tom, jak načíst informace o využití prostředků z zásobník Azure najdete v tématu [využití a cenách služby Azure zásobníku](/azure-stack-billing-and-chargeback.md).
