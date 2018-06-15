---
title: Azure platebních zpracování plán, podle kterého – požadavky na přístup
description: PCI DSS požadavek 7
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: fb16a7973022421525e13313f53f615120caa38a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895026"
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>Požadavky na přístup pro kompatibilní se standardem PCI DSS prostředí 
## <a name="pci-dss-requirement-7"></a>PCI DSS požadavek 7

**Omezení přístupu k datům držitele karty podle obchodních potřeb vědět**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Zajistit, že důležitá data můžete přistupovat pouze oprávněný personál systémy a procesy, musíte mít zavedenou k omezení přístupu na základě na potřeba znát a podle pracovní náplně.

"Potřebujete vědět" je při pouze minimální množství dat a oprávněními potřebnými k provedení úlohy se mají udělit přístupová práva.

## <a name="pci-dss-requirement-71"></a>PCI DSS požadavek 7.1

**7.1** omezit přístup k součástem systému a dat držitele karty pouze uživatelé, jejichž povinnostem vyžaduje takový přístup.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure vynucuje existující zásady ISMS ohledně Azure pracovníky přístup k součástem Azure systému, ověření účinnosti řízení přístupu, poskytuje těsně za běhu přístup pro správu, odvolání přístupu, když už nebude potřebné a zajistit zaměstnanci přístup k prostředí platformy Azure mají obchodní potřeby. Azure přístup k prostředí zákazníků je vysoce omezený a povolen pouze pro schválení od zákazníka.<br /><br />Postupy byly vytvořeny omezit fyzický přístup k datovému centru autorizovaný zaměstnanci, dodavatelům, dodavatelů a návštěvníky. Ověření zabezpečení a vrácení se změnami jsou vyžadovány pro pracovníky nutnosti dočasný přístup k funkci vnitřních dat center. Fyzický přístup k protokolům jsou kontrolovány každé čtvrtletí Azure týmy. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností omezení přístup k součástem systému a dat držitele karty pouze uživatelé, jejichž povinnostem vyžaduje takový přístup zákazníků. To zahrnuje, omezení a omezení přístupu k portálu pro správu Azure a také zadáte účty nebo role s oprávněním k vytváření, upravíte nebo odstraníte PaaS služby.|



### <a name="pci-dss-requirement-711"></a>PCI DSS požadavek 7.1.1

**7.1.1** access definovat musí pro každou roli, včetně:
- Systémové součásti a data prostředky, které jednotlivé role potřebuje přístup k jejich funkce úlohy
- Úroveň oprávnění (například uživatele, správce, atd.) pro přístup k prostředkům

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro definování a dokumentace proces schválení ID uživatele, definování nejnižšími oprávněními, omezuje přístup k datům držitele karty, pomocí jedinečné ID, zajišťuje oddělení povinností a odvolání přístupu uživatele, když už nebude potřebné zákazníků.|



### <a name="pci-dss-requirement-712"></a>PCI DSS požadavek 7.1.2

**7.1.2** omezit přístup k privilegovanému uživateli ID na nejnižší oprávnění potřebná k provedení pracovní náplně.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure přijal zásady použít podnikové a organizační zabezpečení, včetně zásad zabezpečení informací. Zásady schválených, publikovat a přenést do služby Windows Azure. Zásady zabezpečení Microsoft Azure informace vyžaduje, aby být uděleno podle obchodního oprávnění s vlastník majetku autorizace a na základě "potřeba vědět" a "minimálními oprávněními" zásady přístupu k prostředkům Microsoft Azure. Zásady také řeší požadavky na přístup správy životního cyklu, včetně přístupu pro zřizování a autorizaci přístupu, ověřování odebrání přístupová práva, a na pravidelné přístup. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Během nasazení Contoso Webstore vytvoří tři účty: admin, sqladmin a edna (výchozí uživatelská přihlášení k webové aplikaci během provádění ukázku). Role uživatele je omezen na povinností podle zdokumentovaných ukázkový scénář.|



### <a name="pci-dss-requirement-713"></a>PCI DSS požadavek 7.1.3

**7.1.3** přiřadit přístup na základě jednotlivých pracovníky klasifikace úlohy a funkce.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Během nasazení Contoso Webstore vytvoří tři účty: admin, sqladmin a edna (výchozí uživatelská přihlášení k webové aplikaci během provádění ukázku). Role uživatele je omezen na povinností podle zdokumentovaných ukázkový scénář.|



### <a name="pci-dss-requirement-714"></a>PCI DSS požadavek 7.1.4

**7.1.4** vyžadují schválení zdokumentovaný oprávnění zúčastněným stranám zadání požadovaná oprávnění.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností omezení přístup k součástem systému a dat držitele karty pouze uživatelé, jejichž povinnostem vyžaduje takový přístup zákazníků. To zahrnuje, omezení a omezení přístupu k portálu pro správu Azure a také zadáte účty nebo role s oprávněním k vytváření, upravíte nebo odstraníte PaaS služby.|



## <a name="pci-dss-requirement-72"></a>PCI DSS požadavek 7.2

**7.2** zavést systém řízení přístupu pro systémy součásti, které omezují přístup založený na uživatele potřebujete vědět a je nastavena na "Odepřít všechny" nejsou povoleny.
Tento systém řízení přístupu musí zahrnovat následující:
- 7.2.1 pokrytí všech součástí systému.
- 7.2.2 přiřazení oprávnění pro jednotlivce na základě klasifikace úlohy a funkce.
- 7.2.3 výchozí nastavení "Zakázat všechna".

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Active Directory omezit přístup jenom určení uživatelé. Další informace najdete v tématu [pokyny PCI - Identity Management](payment-processing-blueprint.md#identity-management).|



## <a name="pci-dss-requirement-73"></a>PCI DSS požadavek 7.3

**7.3** zajistěte, aby zásady zabezpečení a provozních postupů pro omezení přístupu k datům držitele karty jsou popsané v použití a ví, že všechny dotčené strany.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Případ použití a popis týkající se kdo používá CHD a použití CHD jsou uvedeny v dokumentaci Contoso Webstore.|




