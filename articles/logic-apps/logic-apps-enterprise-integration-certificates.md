---
title: Zabezpečení zpráv B2B s certifikáty v Azure Logic Apps | Microsoft Docs
description: Přidejte certifikáty pro zabezpečení zpráv B2B s Enterprise integračního balíčku
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 7ba76a15792fe40b2a628b030f06930641d197a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299330"
---
# <a name="secure-b2b-messages-with-certificates"></a>Zabezpečení zpráv B2B s certifikáty

Někdy je třeba udržovat v tajnosti komunikace B2B. Pro zajištění komunikace B2B rozlehlé sítě integrace aplikace, konkrétně logiku aplikace, můžete přidat certifikáty ke svému účtu integrace. Certifikáty jsou digitální dokumenty, které ověření identity účastníky v elektronické komunikace.
Certifikáty pomáhá vám zabezpečit komunikaci těmito způsoby:

* Šifrování obsahu zprávy
* Digitální podpis zpráv  

Tyto certifikáty můžete používat ve svých aplikacích integrace enterprise:

* Veřejné certifikáty, které je třeba zakoupit od certifikační autority (CA).
* Privátní certifikáty, které můžete zadat sami. Tyto certifikáty se někdy označují jako certifikáty podepsané svým držitelem.

## <a name="upload-a-public-certificate"></a>Nahrát veřejného certifikátu

Použít *veřejný certifikát* v logiku aplikace, které mají B2B funkce, musíte nejprve nahrát certifikát ke svému účtu integrace. Po definování vlastnosti v [smlouvy](logic-apps-enterprise-integration-agreements.md) , vytvoříte, certifikát je k dispozici a pomáhá vám zabezpečit zpráv B2B.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V hlavní nabídce Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "integraci" a potom vyberte **účty pro integraci**.

   ![Najít váš účet integrace](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. V části **účty pro integraci**, vyberte účet integrace, ve které chcete přidat certifikát.

   ![Vyberte účet integrace, do které chcete přidat certifikát](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. Vyberte **certifikáty** dlaždici.  

   ![Zvolte "Certifikáty"](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. V části **certifikáty**, zvolte **přidat**.

   ![Zvolte "Přidat"](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. V části **přidat certifikát**, zadejte podrobnosti pro svůj certifikát.
   
   1. Zadejte svůj certifikát **název**. Typ certifikátu, vyberte **veřejné**.

   2. Na pravé straně **certifikát** vyberte ikonu složky. 
   Najděte a vyberte soubor certifikátu, který chcete nahrát. 
   Až budete hotoví, zvolte **OK**.

      ![Nahrát veřejný certifikát](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure odešle certifikát po ověření váš výběr.

   ![V tématu nový certifikát](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>Nahrát privátní certifikát

Použít *privátní certifikát* v logiku aplikace, které mají B2B funkce, musíte nejprve nahrát certifikát ke svému účtu integrace. Také je potřeba mít privátní klíč, který je nejprve přidat do [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Po definování vlastnosti v [smlouvy](logic-apps-enterprise-integration-agreements.md) , vytvoříte, certifikát je k dispozici a pomáhá vám zabezpečit zpráv B2B.

> [!NOTE]
> Pro privátní certifikáty, ujistěte se, že přidáte odpovídající veřejný certifikát zobrazí v [smlouvy AS2](logic-apps-enterprise-integration-as2.md) odesílat a přijímat nastavení pro podepisování a šifrování zpráv.

1. [Přidejte svůj privátní klíč do Azure Key Vault](../key-vault/key-vault-get-started.md#add) a poskytují **název klíče**.
   
2. Povolit Azure Logic Apps k provádění operací v Azure Key Vault. K udělení přístupu k objektu služby Logic Apps, použijte příkaz prostředí PowerShell [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), například:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

4. V hlavní nabídce Azure, vyberte **všechny služby**. Do vyhledávacího pole zadejte "integraci" a potom vyberte **účty pro integraci**.

   ![Najít váš účet integrace](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. V části **účty pro integraci**, vyberte účet integrace, ve které chcete přidat certifikát.

6. Vyberte **certifikáty** dlaždici.  

   ![Vyberte dlaždici certifikáty](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. V části **certifikáty**, zvolte **přidat**.   

   ![Klepněte na tlačítko Přidat](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. V části **přidat certifikát**, zadejte podrobnosti pro svůj certifikát.
   
   1. Zadejte svůj certifikát **název**. Typ certifikátu, vyberte **privátní**.

   2. Na pravé straně **certifikát** vyberte ikonu složky. 
   Najděte a vyberte soubor certifikátu, který chcete nahrát. 
   Kromě toho **skupiny prostředků**, **Key Vault**, a **název klíče**. 
   Až budete hotoví, zvolte **OK**.

      ![Přidání certifikátu](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure odešle certifikát po ověření váš výběr.

   ![V tématu nový certifikát](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>Další postup

* [Vytvoření smlouvy s B2B](logic-apps-enterprise-integration-agreements.md)
