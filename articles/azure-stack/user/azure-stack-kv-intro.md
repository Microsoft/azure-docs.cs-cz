---
title: Úvod do služby Azure Stack Key Vault | Dokumentace Microsoftu
description: Zjistěte, jak Azure Stack Key Vault spravovat klíče a tajné kódy
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: a6b4e8c3543d4681c92fbbde30eec0a543fcb0fd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139614"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Úvod do služby Key Vault ve službě Azure Stack

## <a name="prerequisites"></a>Požadavky 

* Můžete musí přihlásit k odběru nabídky, která zahrnuje službu Azure Key Vault.  
* [PowerShell je nakonfigurován pro použití s Azure Stackem](azure-stack-powershell-configure-user.md).
 
## <a name="key-vault-basics"></a>Základní informace o službě Key Vault
Key Vault ve službě Azure Stack pomáhá chránit kryptografické klíče a tajné kódy, které cloudové aplikace a služby používat. Pomocí služby Key Vault můžete šifrovat klíče a tajné kódy, jako například:
   * Ověřovací klíče 
   * Klíče účtu úložiště
   * Šifrovací klíče dat
   * soubory .pfx
   * Hesla

Key Vault zjednodušuje proces správy klíčů a zajišťuje vám kontrolu nad klíči, které se používají k přístupu a šifrování dat. Vývojáři můžou během pár minut vytvořit klíče pro vývoj a testování a potom je bez problémů migrovat na produkční klíče. Správci zabezpečení můžou klíčům podle potřeby udělovat (a odvolávat) oprávnění.

Každý, kdo má předplatnému Azure Stack můžete vytvořit a použít trezorům klíčů. Přestože je Key Vault přínosný pro vývojáře a správce zabezpečení, operátor, který spravuje ostatní služby Azure Stack pro organizaci můžete implementovat a spravovat ho. Azure Stack operátor se můžete přihlásit pomocí předplatného služby Azure Stack, například vytvoření trezoru pro organizace, ve kterém chcete ukládat klíče a potom mít na starost pro tyto provozní úlohy:

* Vytvoření nebo import klíče nebo tajného klíče.
* Odvolání nebo odstranění klíče nebo tajného klíče.
* Autorizace uživatelů nebo aplikací přístup k trezoru klíčů, takže se potom mohli spravovat nebo používat jeho klíče a tajné kódy.
* Konfigurace používání klíčů (například podepisování nebo šifrování).

Operátor, který jim pak můžou vývojáři pomocí jednotné Resource Identifier (identifikátory URI) pro volání ze svých aplikací. Operátory můžete také poskytnout informace o protokolování využití key správci zabezpečení.

Vývojáři také mohou spravovat klíče přímo, pomocí rozhraní API. Další informace najdete v příručce vývojáře pro Key Vault.

## <a name="scenarios"></a>Scénáře
Následující scénáře popisují, jak může Key Vault pomoci splnit potřeby vývojářů a správci zabezpečení.

### <a name="developer-for-an-azure-stack-application"></a>Pro vývojáře pro aplikace Azure Stack
**Problém:** chci napsat aplikaci pro službu Azure Stack, která k podepisování a šifrování používá klíče. Chci, aby tyto klíče musí být mimo aplikaci, tak, aby toto řešení je vhodné pro geograficky distribuovanou aplikaci.

**Příkaz:** klíče jsou uložené v trezoru a vyvolávat identifikátor URI, pokud je nepotřebujete.

### <a name="developer-for-software-as-a-service-saas"></a>Vývojář softwaru jako služby (SaaS)
**Problém:** nechci odpovědnosti odpovědnosti nebo potenciál pro tohoto zákazníka klíče a tajné kódy. Chci, aby zákazníci sami vlastnili a spravovali svoje klíče, aby mi umožňuje soustředit se na to, co umím nejlépe, který poskytuje základní funkce softwaru.

**Příkaz:** zákazníci můžou svoje klíče importovat do služby Azure Stack a je spravovat. 

### <a name="chief-security-officer-cso"></a>Chief Security Officer (CSO)
**Problém:** chci, aby se zajistilo, že Moje organizace má kontrolu nad životním cyklem klíče a může sledovat jejich používání.

**Příkaz:** Key Vault je navržený tak, aby Microsoft neznala ani neextrahuje vaše klíče. Když aplikace potřebuje provést kryptografické operace pomocí klíčů zákazníků, Key Vault používá klíčů jménem aplikace. Aplikace klíče zákazníků nezná. Přestože používáme více služeb Azure Stack a prostředků, mohou spravovat klíče z jednoho umístění ve službě Azure Stack. Trezor poskytuje jednotné rozhraní – bez ohledu na to, kolik trezorů máte ve službě Azure Stack, ve kterých oblastech, podpory a aplikace, které používají.

## <a name="next-steps"></a>Další postup

* [Správa služby Key Vault ve službě Azure Stack pomocí portálu](azure-stack-kv-manage-portal.md)  
* [Správa služby Key Vault ve službě Azure Stack pomocí prostředí PowerShell](azure-stack-kv-manage-powershell.md)

