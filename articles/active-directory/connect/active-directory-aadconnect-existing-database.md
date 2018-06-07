---
title: Nainstalujte Azure AD Connect pomocí existující databáze ADSync | Microsoft Docs
description: Toto téma popisuje postup použití existující databáze ADSync.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: b28d4cc6b10b0fd47a67cfefa7cf1022921a5d96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591765"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Nainstalujte Azure AD Connect použití existující databáze ADSync
Azure AD Connect vyžaduje databázi SQL serveru k ukládání dat. Můžete buď použít výchozí nastavení, SQL Server 2012 Express LocalDB nainstalované službou Azure AD Connect nebo používat úplnou verzi systému SQL. Dříve při instalaci Azure AD Connect vytvoření nové databáze s názvem ADSync vždy. S Azure AD Connect verze 1.1.613.0 (nebo po) máte možnost nainstalovat Azure AD Connect odkazující na existující databáze ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Výhody použití existující databáze ADSync
Podle odkazující na existující databáze ADSync:

- S výjimkou informace pověření je konfigurace synchronizace, které jsou uloženy v databázi ADSync (včetně vlastní synchronizační pravidla, konektory, filtrování a konfigurace volitelných funkcí) automaticky obnovit a použít během instalace . Přihlašovací údaje používané k synchronizaci změn přes Azure AD Connect s místním AD a Azure AD jsou zašifrované, přičemž můžete přistupovat pouze předchozí server Azure AD Connect.
- Také se obnoví všechny identity data (přidružená prostor konektoru a úložiště metaverse) a soubory cookie synchronizace uloženy v databázi ADSync. Nově nainstalovaný Azure AD Connect, který server pokračovat v synchronizaci z kde předchozí server Azure AD Connect skončil, místo nutnosti potřeba provést úplnou synchronizaci.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Scénáře, kde použití existující databáze ADSync je výhodné
Tyto výhody jsou užitečné v následujících scénářích:


- Máte existující nasazení Azure AD Connect. Existující server Azure AD Connect přestane pracovat, ale stále funguje SQL server obsahující databáze ADSync. Můžete nainstalovat nový server Azure AD Connect a přejděte na existující databáze ADSync. 
- Máte existující nasazení Azure AD Connect. SQL server obsahující databáze ADSync již není funkční. Máte ale poslední zálohování databáze. Databáze ADSync na nový server SQL můžete obnovit nejdřív. Po, které můžete nainstalovat nový server Azure AD Connect a přejděte na obnovené databáze ADSync.
- Máte stávající nasazení Azure AD Connect, které používá LocalDB. Z důvodu omezení 10 GB, způsobené LocalDB chcete migrovat do úplné SQL. Můžete zálohovat databáze ADSync z LocalDB a obnovte ji k systému SQL server. Po jejímž uplynutí se můžete znovu nainstalovat nový server Azure AD Connect a přejděte na obnovené databáze ADSync.
- Pokoušíte se nastavit na testovacím serveru a chce Ujistěte se, že její konfigurace se shoduje s aktuální aktivní server. Můžete zálohovat databáze ADSync a obnovte ji na jiný server SQL. Po jejímž uplynutí se můžete znovu nainstalovat nový server Azure AD Connect a přejděte na obnovené databáze ADSync.

## <a name="prerequisite-information"></a>Informace o požadavcích

Než budete pokračovat, Všimněte si z důležité poznámky pro trvat:

- Ujistěte se, že Zkontrolujte požadavky na instalaci Azure AD Connect na Hardware a požadavky a účet a oprávnění potřebná pro instalaci Azure AD Connect. Oprávnění potřebná pro instalaci Azure AD Connect s použitím "použití existující databáze" režim je stejný jako "vlastní" instalace.
- Nasazení Azure AD Connect s existující ADSync databáze je podporována pouze s úplnou SQL. Není podporován v SQL Express LocalDB. Pokud máte existující databázi ADSync v LocalDB, který chcete použít, musíte nejprve zálohování databáze ADSync (LocalDB) a obnovte ji úplné SQL. Po, které můžete nasadit Azure AD Connect s obnovenou databázi pomocí této metody.
- Verze služby Azure AD Connect použitý k instalaci, musí splňovat následující kritéria:
    - 1.1.613.0 nebo vyšší, a
    - Stejná nebo vyšší než verze služby Azure AD Connect naposledy použila databáze ADSync. Pokud Azure AD Connect verze použitý k instalaci je vyšší než verze naposledy použila databáze ADSync, může se vyžadovat úplnou synchronizaci.  Úplná synchronizace je vyžadován, pokud jsou pravidla změny schématu nebo synchronizace mezi dvěma verzemi. 
- Databáze ADSync používá by měl obsahovat stavu synchronizace, který je relativně nové. Poslední aktivita synchronizace s existující databáze ADSync by měla být v rámci poslední tři týdny.
- Při instalaci Azure AD Connect s použitím "použití existující databáze" metoda, není zachována metoda přihlašování nakonfigurované na předchozí server Azure AD Connect. Navíc můžete nelze konfigurovat metoda přihlašování během instalace. Metoda přihlašování můžete konfigurovat pouze po dokončení instalace.
- Nemůže mít více servery Azure AD Connect, které sdílejí stejnou databázi ADSync. Metoda "použití existující databáze" vám umožní znova využít existující databáze ADSync při nový server Azure AD Connect. Nepodporuje sdílení.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Postup instalace Azure AD Connect s režimem "použití existující databáze"
1.  Stáhněte si instalační program služby Azure AD Connect (AzureADConnect.MSI) do systému Windows server. Klikněte dvakrát na Instalační služby Azure AD Connect zahájíte instalaci Azure AD Connect.
2.  Po dokončení instalace Instalační služby MSI spustí se Průvodce Azure AD Connect s instalačním programem režimu Express. Na obrazovce zavřete kliknutím na ikonu ukončení.
![Vítej](media/active-directory-aadconnect-existing-database/db1.png)
3.  Spuštění nového příkazového řádku nebo relaci prostředí PowerShell. Přejděte do složky <drive>\program files\Microsoft Azure AD Connect. Spusťte příkaz.\AzureADConnect.exe /useexistingdatabase spusťte Průvodce Azure AD Connect v režimu instalace "Použití existující databáze".
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
4.  Se zobrazí s Vítá vás Azure AD Connect obrazovky. Jakmile vyjadřujete souhlas s licenčními podmínkami a Všimněte si, ochrany osobních údajů, klikněte na možnost **pokračovat**.
![Vítej](media/active-directory-aadconnect-existing-database/db3.png)
5.  Na **instalace požadovaných součástí** obrazovky, **použít existující Server SQL** je povolena možnost. Zadejte název serveru SQL Server, který je hostitelem databáze ADSync. Pokud modul instance SQL používané k hostování databáze ADSync není výchozí instance systému SQL server, musíte zadat název instance SQL modul. Dál platí Pokud není povoleno procházení SQL, musí také zadáte číslo portu modul instance SQL. Příklad:         
![Vítej](media/active-directory-aadconnect-existing-database/db4.png)           

6.  Na **připojit k Azure AD** obrazovky, je nutné zadat přihlašovací údaje globálního správce adresáře Azure AD. Doporučuje se použít účet ve výchozí domény onmicrosoft.com. Tento účet slouží jenom k vytvoření účtu služby v Azure AD, a po dokončení průvodce se už nepoužívá.
![Připojení](media/active-directory-aadconnect-existing-database/db5.png)
 
7.  Na **připojení adresářů** obrazovce stávající doménové struktuře AD konfigurován pro synchronizaci adresáře je označené ikonou red mezi u ní. Synchronizaci změn z místní doménové struktuře AD, účet služby AD DS je potřeba. Průvodce Azure AD Connect nelze načíst pověření účtu služby AD DS uloženy v databázi ADSync, protože přihlašovací údaje jsou šifrované a mohou ho dešifrovat jenom předchozí server Azure AD Connect. Klikněte na tlačítko **změnit pověření** k zadání účtu služby AD DS pro doménovou strukturu AD.
![Adresáře](media/active-directory-aadconnect-existing-database/db6.png)
 
 
8.  V dialogovém okně automaticky otevírané okno, můžete buď (i), zadejte pověření správce podniku a nechat Azure AD Connect za vás vytvořit účet služby AD DS, nebo účet služby AD DS vytvořit sami (ii) a zadejte své přihlašovací údaje Azure AD Connect. Jakmile jste vybrali možnost a poskytují nezbytná pověření, klikněte na možnost **OK** automaticky otevírané okno dialog zavřete.
![Vítej](media/active-directory-aadconnect-existing-database/db7.png)
 
 
9.  Jakmile jsou přihlašovací údaje poskytnuté, ikonu mezi red se nahradí ikonou zelená značka. Klikněte na **Další**.
![Vítej](media/active-directory-aadconnect-existing-database/db8.png)
 
 
10. Na **připraveno ke konfiguraci** obrazovky, klikněte na tlačítko **nainstalovat**.
![Vítej](media/active-directory-aadconnect-existing-database/db9.png)
 
 
11. Po dokončení instalace se server Azure AD Connect je automaticky povolené pro pracovní režim. Je vhodné zkontrolovat konfiguraci serveru a čekajících exportů neočekávané změny před jeho zakázáním pracovní režim. 

## <a name="next-steps"></a>Další postup

- Nyní, když máte nainstalovanou službu Azure AD Connect, můžete si [ověřit instalaci a přiřadit licence](active-directory-aadconnect-whats-next.md).
- Podrobněji se seznamte s těmito funkcemi, které byly povoleny v rámci instalace: [Prevence náhodného smazání](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) a [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).
- Zjistěte více o těchto běžných tématech: [plánovač a jak aktivovat synchronizaci](active-directory-aadconnectsync-feature-scheduler.md).
- Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
