---
title: 'Azure AD Connect: Řešení potíží s objekt synchronizace | Dokumentace Microsoftu'
description: Toto téma popisuje kroky pro řešení potíží s synchronizace objektů pomocí úlohy řešení potíží.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ed13014dd5ad559ed3793ff52ae410be9174ae2b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461310"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Řešení potíží s synchronizace objektů pomocí synchronizace Azure AD Connect
Tento článek popisuje kroky pro řešení potíží s synchronizace objektů pomocí úlohy řešení potíží. Podívejte se na řešení potíží s fungování v Azure Active Directory (Azure AD) Connect najdete [Toto krátké video](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Úlohy řešení potíží
Pro službu Azure AD Connect nasazení s verzí 1.1.749.0 nebo vyšší, pomocí úlohy řešení potíží v Průvodci potíží se synchronizací objektu. U starších verzí, prosím vyřešit ručně, jak je popsáno [tady](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Spuštění úlohy řešení potíží v Průvodci
Ke spuštění úlohy řešení potíží v průvodci, proveďte následující kroky:

1.  Otevřete novou relaci prostředí Windows PowerShell na vašem serveru Azure AD Connect s spustit jako správce.
2.  Spustit `Set-ExecutionPolicy RemoteSigned` nebo `Set-ExecutionPolicy Unrestricted`.
3.  Spusťte Průvodce Azure AD Connect.
4.  Přejděte na stránku pro další úlohy, vyberte Poradce při potížích a klikněte na tlačítko Další.
5.  Na stránce Poradce při potížích s kliknutím na tlačítko spustit Poradce při potížích nabídky start v prostředí PowerShell.
6.  V hlavní nabídce vyberte řešení potíží se synchronizací objektu.
![](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Řešení potíží s vstupní parametry
Následující vstupní parametry jsou vyžadované úlohy řešení potíží:
1.  **Rozlišující název objektu** – jde o rozlišující název objektu, který potřebuje řešení potíží
2.  **Název konektoru Active Directory** – jde o název doménové struktury AD, ve které se nachází nad objektu.
3.  Přihlašovací údaje globálního správce tenanta Azure AD ![](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Vysvětlení výsledky úlohy řešení potíží
Řešení potíží úloha provede následující kontroly:

1.  Zjištění neshody hlavní název uživatele, pokud objekt se synchronizuje s Azure Active Directory
2.  Zaškrtněte, pokud objekt je filtrován kvůli filtrování domény
3.  Zaškrtněte, pokud objekt je filtrovaná kvůli filtrování podle organizačních jednotek
4.  Zaškrtněte, pokud objekt synchronizace blokovaný z důvodu propojená poštovní schránka
5. Zkontrolujte, jestli je objekt dynamické distribuční skupiny, které by nemělo být synchronizovány

Zbytek tohoto oddílu popisuje konkrétní výsledky, které jsou vrácené úlohy. V každém případě obsahuje úlohu analýzu a doporučené akce k vyřešení daného problému.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Zjištění neshody hlavní název uživatele, pokud objekt se synchronizuje s Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Přípona UPN je Neověřeno s Tenantem Azure AD
Když UserPrincipalName (UPN) / přípona alternativního přihlašovacího ID není ověřená s Tenantem Azure AD a Azure Active Directory nahrazuje přípony UPN s názvem domény výchozí "onmicrosoft.com".

![](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Změna přípona UPN z jedné federované domény na jinou federovanou doménu
Azure Active Directory neumožňuje synchronizace UserPrincipalName (UPN) nebo změnit příponu alternativního přihlašovacího ID z jedné federované domény na jiný federované domény. To platí pro domény, které se ověřují pomocí Tenanta služby Azure AD a ověřování typu jako federativní.

![](media/tshoot-connect-objectsync/objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD Tenant DirSync "SynchronizeUpnForManagedUsers" je zakázaná
Při vypnuté funkci DirSync Tenanta služby Azure AD "SynchronizeUpnForManagedUsers" Azure Active Directory není povolena synchronizace aktualizace UserPrincipalName/alternativní přihlašovací ID pro licencované uživatelské účty s spravované ověřování.

![](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objekt je filtrován kvůli filtrování domény
### <a name="domain-is-not-configured-to-sync"></a>Doména není nakonfigurována pro synchronizaci
Objekt je mimo rozsah z důvodu naprostou konfiguraci domény. Objekt v následujícím příkladu je synchronizovaný oboru, jak jsou vyfiltrovaná domény, který patří do ze synchronizace.

![](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Doména je nakonfigurována k synchronizaci, ale chybí kroky profily spuštění nebo spuštění
Objekt je mimo rozsah jako domény nebylo nalezeno spustit kroky profily nebo spuštění. V následujícím příkladu je objekt synchronizovaný oboru jako patřící do domény chybí spuštění kroky pro úplný Import profilu spustit.
![](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Objekt je filtrovaná kvůli filtrování podle organizačních jednotek
Objekt je synchronizovaný oboru kvůli konfiguraci filtrování organizačních jednotek. V následujícím příkladu objekt náleží do organizační jednotky = NoSync, DC = bvtadwbackdc, DC = com.  Rozsah synchronizace není součástí této organizační jednotky.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problém propojená poštovní schránky
Propojená poštovní schránka by měla být přidružená s externí hlavní účet nachází v jiné doménové struktuře pro důvěryhodného účtu. Pokud není žádný takový externí hlavní účet a Azure AD Connect nebude synchronizace uživatele účtu odpovídá propojená poštovní schránky v doménové struktuře serveru Exchange do tenanta služby Azure AD.</br>
![Propojená poštovní schránka](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Dynamická skupina distribuční problém
Z důvodu různých rozdíly mezi místní služby Active Directory a Azure Active Directory, Azure AD Connect nesynchronizuje dynamické distribuční skupiny do tenanta služby Azure AD.

![Dynamické distribuční skupiny](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Sestavu ve formátu HTML
Kromě analýzy objektu úlohy řešení potíží také vygeneruje zprávu ve formátu HTML, který obsahuje všechno, co vědět o objektu. Je možné sdílet tuto sestavu ve formátu HTML s tým podpory, proveďte další řešení potíží v případě potřeby.

![](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
