---
title: 'Azure AD Connect: Co je modul Powershellu ADConnectivityTool | Dokumentace Microsoftu'
description: Toto téma představuje nový modul prostředí ADConnectivity PowerShell
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52f8b70a4b098cc7266042499300235588e5303f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164307"
---
# <a name="what-is-the-adconnectivitytool-powershell-module"></a>Co je modul Powershellu ADConnectivityTool?

Nástroj ADConnectivity je modul Powershellu, který se používá v jednom z následujících akcí:

- Během instalace, pokud je problém se síťovým připojením úspěšném ověření přihlašovacích údajů Active Directory zabrání uživateli v průvodci k dispozici.
- Po dokončení instalace uživatelem, který volá funkce z relace prostředí PowerShell.

Nástroj je umístěný v: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool během instalace

Na **připojení k adresářům** stránky, v Azure AD Connect průvodce, dojde k problému v síti, budou ADConnectivityTool automaticky použijte jednu z jejích funkcí k určení, co se děje.  Kterýkoli z následujících lze považovat za problémy se sítí:

- Název doménové struktury zadaného uživatele byl zadán nesprávně nebo uvedená doménová struktura neexistuje 
- UDP portu 389 je uzavřen do řadiče domény přidružené k doménové struktuře uživatele
- Přihlašovací údaje zadané v okně účet doménové struktury AD nemá oprávnění k načtení řadiče domény přidružené k cílové doménové struktuře
- Všechny porty TCP 53, 88 nebo 389 jsou uzavřeny v řadičích domény přidružené k doménové struktuře uživatele 
- Jak UDP 389 a TCP port (nebo porty) jsou uzavřeny.
- DNS nebylo možné přeložit k zadané doménové struktury a její přidružené řadiče domény

Pokaždé, když najde některý z těchto problémů, zobrazí se v Průvodci AADConnect související chybová zpráva:


![Chyba](media/how-to-connect-adconnectivitytools/error1.png)

Například když jsme se pokoušíte přidat adresář na **připojení k adresářům** obrazovky, Azure AD Connect je potřeba to ověřit a očekává, že budete moci komunikovat s řadičem domény prostřednictvím portu 389.  V případě nedostupnosti jsme se zobrazí chyba, ke které je zobrazena ve výše uvedeném snímku obrazovky.  

Co se skutečně děje na pozadí je, že je volání služby Azure AD Connect `Start-NetworkConnectivityDiagnosisTools` funkce.  Tato funkce je volána, když se ověřování přihlašovacích údajů nezdaří z důvodu problém síťového připojení.

Nakonec je vygenerován soubor podrobnému protokolu pokaždé, když nástroj je volána z průvodce. V protokolu se nachází v **C:\ProgramData\AADConnect\ADConnectivityTool-<date>-<time>.log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools po dokončení instalace
Po instalaci služby Azure AD Connect, můžete použít jakékoli funkce v modulu ADConnectivityTools PowerShell.  

Referenční informace týkající se funkcí v [ADConnectivityTools odkaz](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Budeme provádět volání této funkce vzhledem k tomu může **pouze** ručně volat, poté, co ADConnectivityTool.psm1 byla naimportována do prostředí PowerShell. 

Tato funkce provádí stejnou logiku, na kterém běží Průvodce připojení Azure AD k ověření zadané přihlašovací údaje služby AD.  Ale nabízí mnohem podrobnější vysvětlení o problému a navrhované řešení. 

Ověření připojení se skládá z následujících kroků:
-   Získání objektu domény plně kvalifikovaný název domény (plně kvalifikovaný název domény)
-   Ověřte, že pokud uživatel vybral "Vytvořit nové reklamy účet", tyto přihlašovací údaje patří do skupiny Enterprise Administrators
-   Objekt plně kvalifikovaný název domény doménové struktury GET
-   Potvrďte, že aspoň jednu doménu přidružené k dříve získaného objektu plně kvalifikovaný název domény doménové struktury je dostupný
-   Ověřte, že funkční úroveň doménové struktury Windows Server 2003 nebo vyšší.

Uživatel bude moct přidat adresář, pokud tyto akce byly provedeny úspěšně.

Pokud uživatel spustí tuto funkci po problém je vyřešen (nebo pokud neexistuje žádný problém vůbec) bude výstup uvádět uživatel chce přejít zpět do Azure AD Connect průvodce a pokuste se vložit přihlašovací údaje znovu.



## <a name="next-steps"></a>Další kroky
- [Azure AD Connect: Účty a oprávnění](reference-connect-accounts-permissions.md)
- [Expresní instalace](how-to-connect-install-express.md)
- [Vlastní instalace](how-to-connect-install-custom.md)
- [Odkaz na ADConnectivityTools](reference-connect-adconnectivitytools.md)

