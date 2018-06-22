---
title: Používání databází MySQL jako PaaS v zásobníku Azure | Microsoft Docs
description: Zjistěte, jak můžete nasadit MySQL zprostředkovatel prostředků a poskytovat databází MySQL jako službu v Azure zásobníku.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 24ba595413cde07c420a94de234d7926e0eb0e7f
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309838"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Používání databází MySQL v zásobníku Microsoft Azure

Můžete nasadit poskytovatele prostředků MySQL rozhraní API pro používání databází MySQL nasazené v Azure zásobníku. Další informace o poskytovateli prostředků rozhraní API najdete v tématu [Windows Azure Pack MySQL prostředků zprostředkovatele REST referenční dokumentace rozhraní API](https://msdn.microsoft.com/library/dn528442.aspx).

Databáze MySQL společné na webech a podporu mnoha platformách webu. Můžete například vytvořit WordPress weby s využitím webové aplikace platforma jako služba (PaaS) rozšíření.

Poté, co nasadíte poskytovatele prostředků, můžete:

* Vytvoření databáze MySQL servery a databáze, které používají nasazení šablony Azure Resource Manager.
* Zadejte databází MySQL jako služba.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Architektura adaptér zprostředkovatele prostředků MySQL

Poskytovatel prostředků má následující komponenty:

* **MySQL prostředků zprostředkovatele adaptér virtuálního počítače (VM)**, což je virtuální počítač Windows serveru, který běží služby poskytovatele.
* **Zprostředkovatel prostředků**, který zpracovává žádosti a databázi prostředkům přistupuje.
* **Servery, které hostují MySQL Server**, které poskytují kapacitu pro databáze, které se nazývají hostitelské servery. Můžete vytvořit sami instance databáze MySQL nebo poskytovat přístup k externí instance databáze MySQL. [Galerii pro rychlý start Azure zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) má šablonu příklad, který vám pomůže:

  * Vytvoření databáze MySQL serveru za vás.
  * Stáhnout a nasadit MySQL Server v Azure Marketplace.

> [!NOTE]
> Hostitelské servery, které jsou nainstalovány v Azure zásobníku integrované systémy musí být vytvořeny z předplatného klienta. Nemohou být vytvářeny z předplatného výchozího zprostředkovatele. Musí být vytvořený z portálu klienta nebo z relace prostředí PowerShell příslušné přihlášení. Všechny hostitelské servery jsou fakturovatelné virtuální počítače a musí mít licenci. Správce služby můžete být vlastníkem předplatného klienta.

### <a name="required-privileges"></a>Požadovaná oprávnění

Systémový účet musí mít následující oprávnění:

* **Databáze:** vytvořit, vyřadit
* **Přihlášení:** vytvořit, nastavit, vyřadit, udělit, odvolat  

## <a name="next-steps"></a>Další postup

[Nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)
