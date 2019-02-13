---
title: Použití databází MySQL jako PaaS v Azure stacku | Dokumentace Microsoftu
description: Zjistěte, jak můžete nasazení poskytovatele prostředků MySQL a poskytují databází MySQL jako služba v Azure stacku.
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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: e610f946868940f9fde66932bedec1dc998f390b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203875"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Použití databází MySQL ve službě Microsoft Azure Stack

Databáze MySQL se běžně používají s weby a podporují mnoho platforem webu. Například můžete vytvořit pomocí doplňku zprostředkovatele (PaaS) prostředku služby App Services weby s platformou WordPress.

Po nasazení poskytovatele prostředků, můžete:

* Vytvářejte MySQL servery a databáze pomocí šablony nasazení Azure Resource Manageru.
* Poskytují databází MySQL jako služba.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Architektura adaptéru poskytovatele prostředků MySQL

Poskytovatel prostředků má následující komponenty:

* **MySQL prostředků poskytovatele adaptér virtuálního počítače (VM)**, což je virtuální počítač Windows Server, na kterém běží služby poskytovatele.
* **Poskytovatel prostředků**, která zpracovává požadavky a prostředky databáze přístupy.
* **Servery, které hostují MySQL Server**, které poskytují kapacity pro databáze, které se nazývají hostitelské servery. Můžete vytvořit instance MySQL sami, nebo poskytnout přístup k externí instance MySQL. [Galerii pro rychlý start Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) je Ukázková šablona, která vám umožní:

  * Vytvoření serveru MySQL za vás.
  * Stáhnout a nasadit MySQL Server v Azure Marketplace.

> [!NOTE]
> Hostitelské servery, které jsou nainstalované ve službě Azure Stack integrované systémy musí být vytvořené z tenanta předplatného. Nelze vytvořit z výchozí předplatné poskytovatele. Musí se vytvořit na portálu klienta nebo v relaci Powershellu s odpovídající znak v. Všechny hostitelské servery jsou fakturovatelná virtuální počítače a musí mít licence. Správce služby může být vlastníkem předplatného tenanta.

### <a name="required-privileges"></a>Požadovaná oprávnění

Systémový účet musí mít následující oprávnění:

* **Databáze:** vytvořit, vyřadit
* **Přihlášení:** vytvořit, nastavit, vyřadit, udělit, odvolat  

## <a name="next-steps"></a>Další postup

[Nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)
