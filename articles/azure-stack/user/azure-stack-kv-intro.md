---
title: Azure Key Vault zásobníku Úvod | Microsoft Docs
description: Zjistěte, jak Azure zásobníku Key Vault spravuje klíče a tajné klíče
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: mabrigg
ms.openlocfilehash: a50a03e70ccf014a8a9d33e0f177febed560853f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26636722"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Úvod do trezoru klíčů v Azure zásobníku

## <a name="prerequisites"></a>Požadavky 

* Musí se přihlásíte k nabídku, která zahrnuje službu Azure Key Vault.  
* [Prostředí PowerShell je nakonfigurována pro použití s Azure zásobníku](azure-stack-powershell-configure-user.md).
 
## <a name="key-vault-basics"></a>Základy Key Vault
V zásobníku Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče, které cloudové aplikace a služby používat. Pomocí Key Vault můžete šifrovat klíče a tajné klíče, jako například:
   * Ověřovací klíče 
   * Klíče účtu úložiště
   * Šifrovací klíče dat
   * soubory .pfx
   * Hesla

Key Vault zjednodušuje proces správy klíčů a zajišťuje vám kontrolu nad klíči, které se používají k přístupu a šifrování dat. Vývojáři můžou během pár minut vytvořit klíče pro vývoj a testování a potom je bez problémů migrovat na produkční klíče. Správci zabezpečení můžou klíčům podle potřeby udělovat (a odvolávat) oprávnění.

Každý, kdo má předplatné Azure zásobníku můžete vytvořit a použít trezorů klíčů. I když Key Vault přínosný vývojářů a správců zabezpečení, operátor, který spravuje jinými službami Azure zásobníku v organizaci, můžete implementovat a spravovat ji. Například zásobník Azure operátor se můžete přihlásit pomocí předplatného služby Azure zásobníku vytvořit trezor pro organizaci, do kterého chcete ukládat klíče a potom je zodpovědná za tyto provozní úlohy:

* Vytvoření nebo import klíče nebo tajného klíče.
* Odvolání nebo odstranění klíče nebo tajného klíče.
* Autorizace uživatelů nebo aplikace pro přístup k trezoru klíčů, takže se potom mohli spravovat nebo používat jeho klíče a tajné klíče.
* Konfigurace použití klíče (například podepisování nebo šifrování).

Operátor může potom poskytne vývojářům identifikátory Uniform Resource (Identifier) volat ze svých aplikací. Operátory můžete také poskytnout informace o protokolování použití klíče pro správce zabezpečení.

Vývojáři také mohou spravovat klíče přímo, pomocí rozhraní API. Další informace naleznete v příručce pro vývojáře Key Vault.

## <a name="scenarios"></a>Scénáře
Následující scénáře popisují, jak může Key Vault pomoci splnit potřeby vývojářů a správců zabezpečení.

### <a name="developer-for-an-azure-stack-application"></a>Vývojář aplikace Azure zásobníku
**Problém:** chci napsat aplikaci pro Azure zásobníku, která používá klíče pro podepisování a šifrování. Chci, aby tyto klíče být mimo aplikaci, tak, aby je vhodný pro aplikaci, která je geograficky distribuovanou řešení.

**Příkaz:** klíče jsou uložené v trezoru a v případě potřeby vyvolat identifikátoru URI.

### <a name="developer-for-software-as-a-service-saas"></a>Vývojář softwaru jako služby (SaaS)
**Problém:** nechci odpovědnost nebo potenciální odpovědnosti pro klíče a tajné klíče tohoto zákazníka. Chci, aby zákazníci sami vlastnili a spravovali svoje klíče, aby mi umožní soustředit se na to, co umím nejlépe, která poskytuje základní funkce softwaru.

**Příkaz:** zákazníci můžou svoje klíče importovat do zásobníku Azure a následně spravovat je. 

### <a name="chief-security-officer-cso"></a>Ředitel zabezpečení (CSO)
**Problém:** chcete Ujistěte se, že Moje organizace má kontrolu nad životním cyklem klíče a můžete sledovat použití klíče.

**Příkaz:** Key Vault je navržený tak, aby Microsoft neznala ani neextrahovala klíče. Pokud aplikace potřebuje provést kryptografické operace pomocí klíčů zákazníků, Key Vault používá klíče jménem aplikace. Aplikace klíče zákazníků nezná. Přestože používáme více služeb Azure zásobníku a prostředků, můžete spravovat klíče z jednoho místa v zásobníku Azure. Trezor poskytuje jednotné rozhraní – bez ohledu na to, kolik trezorů máte v zásobníku Azure, které oblasti jejich podporu a které aplikace je používají.

## <a name="next-steps"></a>Další kroky

* [Spravovat Key Vault v zásobníku Azure pomocí portálu](azure-stack-kv-manage-portal.md)  
* [Správa Key Vault v zásobníku Azure pomocí prostředí PowerShell](azure-stack-kv-manage-powershell.md)

