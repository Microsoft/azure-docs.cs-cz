---
title: Neočekávaná chyba při provádění souhlasu pro aplikaci | Microsoft Docs
description: Popisuje chyby, které se můžou vyskytnout během procesu souhlasit s aplikace a co můžete dělat o nich
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: bbc0cee8a44773c025c6174eaf7eccaba81b8d1b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Neočekávaná chyba při provádění souhlas k aplikaci

Tento článek popisuje chyby, které se můžou vyskytnout během procesu souhlasit s aplikace. Pokud Poradce při potížích neočekávané souhlasu vyzve není obsahovat chybové zprávy naleznete na adrese [scénáře ověřování pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Mnoho aplikací, které se integrují s Azure Active Directory vyžadují oprávnění pro přístup k jiným prostředkům, aby bylo možné fungovat. Když tyto prostředky jsou také integrované s Azure Active Directory, oprávnění pro přístup k nim je často požadovaná pomocí společného rámce souhlasu. 

To vede výzva k povolení spuštění se zobrazuje, který obvykle probíhá poprvé aplikace se používá, ale může dojít také při následné použití aplikace.

Určitých podmínek musí být pro uživatele a oprávnění, která aplikace vyžaduje souhlas na hodnotu true. Pokud nejsou tyto podmínky splněny, může dojít k různých chybám. Mezi ně patří:

## <a name="requesting-not-authorized-permissions-error"></a>Požaduje chybě není autorizovaný oprávnění
* **AADSTS90093:** &lt;clientAppDisplayName&gt; požaduje jeden nebo více oprávnění, která jste neautorizovaných udělit. Obraťte se na správce, který můžete vyjádřit souhlas. k této aplikaci vaším jménem.

K této chybě dojde, když se uživatel, který není správcem společnosti se pokusí použít aplikaci, která vyžaduje oprávnění, které můžete udělit pouze správce. Tuto chybu můžete vyřešit správcem udělení přístupu k aplikaci jménem své organizace.

## <a name="policy-prevents-granting-permissions-error"></a>Zásada brání udělení oprávnění chyby
* **AADSTS90093:** správce &lt;tenantDisplayName&gt; nastavil zásadu, která brání udělení &lt;název aplikace&gt; vyžaduje oprávnění. Obraťte se na správce systému &lt;tenantDisplayName&gt;, který můžete udělit oprávnění k této aplikaci vaším jménem.

K této chybě dojde, když správce společnosti dojde k vypnutí možnosti pro uživatele o souhlas pro aplikace, pak uživatel není správcem se pokusí použít aplikaci, která vyžaduje souhlas. Tuto chybu můžete vyřešit správcem udělení přístupu k aplikaci jménem své organizace.

## <a name="intermittent-problem-error"></a>Chyba občasný problém
* **AADSTS90090:** vypadá došlo občasný problém záznam jste se pokusili udělte oprávnění &lt;clientAppDisplayName&gt;. Zkuste to znovu později.

Tato chyba znamená, že došlo k problému straně přerušované služby. Dají se vyřešit pokusem o souhlas aplikaci znovu.

## <a name="resource-not-available-error"></a>Prostředek není k dispozici – chyba
* **AADSTS65005:** aplikace &lt;clientAppDisplayName&gt; požadovaná oprávnění pro přístup k prostředkům &lt;resourceAppDisplayName&gt; není k dispozici. 

Obraťte se na vývojáře aplikace.

##  <a name="resource-not-available-in-tenant-error"></a>Prostředek není k dispozici v Chyba klienta
* **AADSTS65005:** &lt;clientAppDisplayName&gt; požaduje přístup k prostředku &lt;resourceAppDisplayName&gt; není k dispozici ve vaší organizaci &lt;tenantDisplayName &gt;. 

Ujistěte se, že tento prostředek je k dispozici nebo se obraťte na správce &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Chybová zpráva o neshodě oprávnění
* **AADSTS65005:** aplikace požadovaný souhlas k přístupu k prostředku &lt;resourceAppDisplayName&gt;. Tento požadavek se nezdařil, protože neodpovídá jak byla předem nakonfigurovaná aplikace při registraci aplikace. Obraťte se na aplikace vendor.* *

Tyto chyby, které všechny dojít při pokusu aplikace uživatele o souhlas vyžaduje oprávnění pro přístup k aplikaci prostředku, který nebyl nalezen v adresáři organizace (klientů). Tato situace může nastat z několika důvodů:

-   Vývojář aplikace klienta má své aplikace nesprávně nakonfigurované, způsobuje ho chcete požadovat přístup k je neplatný prostředek. V takovém případě musí vývojář aplikace aktualizovat konfiguraci aplikace klienta, chcete-li vyřešit tento problém.

-   Instanční objekt reprezentující cílová prostředků aplikace v organizaci, neexistuje nebo existovalo v minulosti, ale byla odebrána. Chcete-li vyřešit tento problém, musí být zřízená objekt služby pro aplikaci prostředků v organizaci, tak klientské aplikace můžete požádat o oprávnění k němu. To může dojít v počtu způsoby, v závislosti na typu aplikace, včetně:

    -   Získávání předplatné prostředků aplikace (Microsoft publikované aplikace)

    -   Souhlas prostředků aplikace

    -   Udělení oprávnění aplikací prostřednictvím portálu Azure

    -   Přidání aplikace z Azure AD Application Gallery

## <a name="next-steps"></a>Další postup 

[Aplikace, oprávnění a souhlasu v Azure Active Directory (koncový bod v1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Obory, oprávnění a souhlasu ve službě Azure Active Directory (koncový bod v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


