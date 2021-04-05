---
title: Ověřování SSH s Azure Active Directory
description: Pokyny pro architekturu při dosahování integrace SSH s Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b77ab0832fa19149c270d6ba5a6641069548cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172716"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) je síťový protokol, který zajišťuje bezpečné šifrování pro síťové služby v nezabezpečené síti. SSH taky poskytuje příkaz pro přihlášení k příkazovému řádku, spouští vzdálené příkazy a bezpečně přenáší soubory. Obvykle se používá v systémech UNIX, jako je Linux®. SSH nahrazuje protokol Telnet, který neposkytuje šifrování v nezabezpečené síti. 

Azure Active Directory (Azure AD) poskytuje rozšíření virtuálního počítače pro systémy Linux® běžící na Azure. 

## <a name="use-when"></a>Kdy použít 

* Práce s virtuálními počítači se systémem Linux®, které vyžadují vzdálené přihlášení

* Spouštění vzdálených příkazů v systémech se systémem Linux®

* Zabezpečený přenos souborů v nezabezpečené síti

![Diagram Azure AD s protokolem SSH](./media/authentication-patterns/ssh-auth.png)

SSH s Azure AD

## <a name="components-of-system"></a>Součásti systému 

* **Uživatel**: SPUSTÍ klienta ssh, aby nastavil připojení k virtuálním počítačům s® Linux a poskytovala přihlašovací údaje pro ověřování.

* **Webový prohlížeč**: součást, se kterou uživatel pracuje. Komunikuje se zprostředkovatelem identity (Azure AD), aby mohl bezpečně ověřit a autorizovat uživatele.

* **Klient SSH**: jednotka procesu nastavení připojení.

* **Azure AD**: ověřuje identitu uživatele pomocí toku zařízení a vydává token pro virtuální počítače se systémem Linux.

* **Virtuální počítač Linux**: přijímá token a poskytuje úspěšné připojení.

## <a name="implement-ssh-with-azure-ad"></a>Implementace SSH s Azure AD 

* [Přihlášení k virtuálnímu počítači se systémem Linux® s přihlašovacími údaji Azure Active Directory – Azure Virtual Machines ](../../virtual-machines/linux/login-using-aad.md) 

* [Tok kódu zařízení OAuth 2,0 – platforma Microsoftu identity ](../develop/v2-oauth2-device-code.md)

* [Integrace s Azure Active Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

