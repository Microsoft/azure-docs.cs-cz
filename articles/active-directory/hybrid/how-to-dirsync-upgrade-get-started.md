---
title: 'Azure AD Connect: Upgrade z nástroje DirSync | Dokumentace Microsoftu'
description: Zjistěte, jak upgradovat z nástroje DirSync na Azure AD Connect. Tento článek popisuje kroky upgradování z nástroje DirSync na Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 12a11e444372ad7004e566851532a98be63af8e8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195354"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: Upgrade z nástroje DirSync
Azure AD Connect je nástupcem nástroje DirSync. V tomto tématu najdete popis způsobů upgradu z nástroje DirSync. Pokud upgradujete z jiné verze služby Azure AD Connect nebo ze služby Azure AD Sync, tyto kroky nefungují.

Před zahájením instalace Azure AD Connect, ujistěte se, že k [stáhnout Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) a provést požadovanou přípravu popsanou kroků [Azure AD Connect: Hardware a nezbytné předpoklady](how-to-connect-install-prerequisites.md). Zejména si přečtěte následující informace, protože se od DirSync liší tyto oblasti:

* Požadovaná verze rozhraní .NET a prostředí PowerShell. Je třeba, aby na serveru byly novější verze, než jaké vyžaduje DirSync.
* Konfigurace proxy serveru. Pokud pro přístup k Internetu používáte proxy server, toto nastavení je třeba konfigurovat ještě před upgradem. DirSync vždy používá proxy server nakonfigurovaný pro uživatele, který provádí instalaci, ale Azure AD Connect používá nastavení platné pro počítač.
* Adresy URL musí být v proxy serveru povolené. Pro základní scénáře, které také DirSync podporuje, jsou požadavky stejné. Pokud chcete používat nové funkce zahrnuté do Azure AD Connect, je třeba povolit některé nové adresy URL.

> [!NOTE]
> Jakmile povolíte novému serveru Azure AD Connect zahájení synchronizace změn do Azure AD, nemůžete se už vrátit k používání DirSync nebo Azure AD Sync. Downgrade z Azure AD Connect na starší verze klientů, včetně nástrojů DirSync a Azure AD Sync, není podporovaný a může vést k problémům, například ke ztrátě dat v Azure AD.

Pokud neupgradujete z nástroje DirSync, přečtěte si [související dokumentaci](#related-documentation) a použijte jiné scénáře.

## <a name="upgrade-from-dirsync"></a>Upgrade z nástroje DirSync
V závislosti na aktuálním nasazení nástroje DirSync můžete upgradovat různými způsoby. Pokud je očekávaná doba upgradu kratší než tři hodiny, pak doporučujeme provést místní upgrade. Pokud je očekávaná doba upgradu delší než tři hodiny, pak doporučujeme provést paralelní nasazení na jiném serveru. Pokud máte více než 50 000 objektů, potrvá upgrade odhadem déle než tři hodiny.

| Scénář |
| --- | --- |
| [Místní upgrade](#in-place-upgrade) |
| [Paralelní nasazení](#parallel-deployment) |

> [!NOTE]
> Pokud plánujete upgrade z nástroje DirSync na službu Azure AD Connect, nesnažte se DirSync odinstalovat sami, dokud neproběhne upgrade. Azure AD Connect bude číst a migrovat konfigurace z nástroje DirSync. Odinstalace proběhne až po kontrole serveru.

**Místní upgrade**  
Očekávaný čas dokončení upgradu se zobrazí v průvodci. Odhad vychází z předpokladu, že upgrade databáze s 50 000 objekty (uživatelé, kontakty a skupiny) potrvá tři hodiny. Pokud je počet objektů v databázi menší než 50 000, Azure AD Connect doporučí místní upgrade. Pokud se rozhodnete pokračovat, budou při upgradu automaticky použita vaše aktuální nastavení a server automaticky obnoví aktivní synchronizaci.

Pokud chcete provést migraci konfigurace a paralelní nasazení, můžete doporučení pro místní upgrade ignorovat. Například můžete při této příležitosti obnovit hardware a operační systém. Další informace najdete v tématu [Paralelní nasazení](#parallel-deployment).

**Paralelní nasazení**  
Pokud počet vašich objektů přesahuje 50 000, doporučuje se použít paralelní nasazení. Tímto nasazením se vyhnete různým provozním zdržením, ke kterým by mohlo u vašich uživatelů dojít. Instalace Azure AD Connect se pokusí odhadnout prostoje při upgradu, ale pokud jste DirSync upgradovali již dříve, raději se řiďte vlastními zkušenostmi.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>Podporované konfigurace nástroje DirSync, které budou upgradovány
Následující změny konfigurace upgradovaný nástroj DirSync podporuje:

* Filtrování domén a organizačních jednotek
* alternativní ID (UPN)
* synchronizace hesla a hybridní nastavení Exchange
* doménová struktura/doména a nastavení služby Azure AD
* filtrování podle atributů uživatele

Následující změnu nelze upgradovat. Pokud máte tuto konfiguraci, upgrade bude zablokován:

* nepodporované změny nástroje DirSync, např. odstraněné atributy nebo použití vlastní rozšířující knihovny DLL.

![Upgradování zablokováno](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

V těchto případech doporučujeme nainstalovat nový server Azure AD Connect v [pracovním režimu](how-to-connect-sync-operations.md#staging-mode) a ověřit starou konfiguraci nástroje DirSync a novou konfiguraci služby Azure AD Connect. Veškeré změny znovu použijte pomocí vlastní konfigurace, jak je popsáno v tématu [Vlastní konfigurace služby Azure AD Connect Sync](how-to-connect-sync-whatis.md).

Hesla, která nástroj DirSync používá pro účty služby, nelze získat a nebudou migrována. Tato hesla se při upgradu resetují.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>Hlavní kroky upgradu z nástroje DirSync na Azure AD Connect.
1. Vítá vás Azure AD Connect
2. Analýza aktuální konfigurace nástroje DirSync
3. Získání hesla globálního správce služby Azure AD
4. Získání přihlašovacích údajů pro účet správce podnikové sítě (použije se pouze během instalace služby Azure AD Connect)
5. Instalace služby Azure AD Connect
   * Odinstalace nástroje DirSync (nebo jeho dočasné vypnutí)
   * Instalace služby Azure AD Connect
   * Volitelné zahájení synchronizace

Další kroky jsou požadovány, pokud:

* Aktuálně používáte plnou instalaci systému SQL Server – místního nebo vzdáleného.
* V rozsahu synchronizace máte více než 50 tisíc objektů.

## <a name="in-place-upgrade"></a>Místní upgrade
1. Spusťte instalační program služby Azure AD Connect (MSI).
2. Zkontrolujte a odsouhlaste licenční podmínky a prohlášení o ochraně osobních údajů.  
   ![Vítá vás služba Azure AD](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. Kliknutím na tlačítko Další zahájíte analýzu stávající instalace nástroje DirSync.  
   ![Analýza stávající instalace nástroje Directory Sync](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. Až analýza proběhne, zobrazí se pokyny, jak pokračovat dál.  
   * Pokud používáte systém SQL Server Express a máte méně než 50 tisíc objektů, zobrazí se následující obrazovka:  
     ![Analýza dokončena, připraveno k upgradu z nástroje DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * Pokud pro nástroj DirSync používáte plnou instalaci SQL Serveru, zobrazí se místo toho tato stránka:  
     ![Analýza dokončena, připraveno k upgradu z nástroje DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     Zobrazí se informace, že DirSync používá stávající databázový server systému SQL Server. V případě potřeby proveďte příslušné změny. Kliknutím na tlačítko **Další** pokračujte v instalaci.
   * Pokud máte více než 50 000 objektů, zobrazí se místo toho tato obrazovka:  
     ![Analýza dokončena, připraveno k upgradu z nástroje DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     Chcete-li pokračovat v upgrade v místě, klikněte na zaškrtávací políčko vedle této zprávy: **Pokračujte v upgradu nástroje DirSync na tomto počítači.**
     Pokud chcete místo toho provést [paralelní nasazení](#parallel-deployment), exportujte nastavení konfigurace nástroje DirSync a přesuňte konfiguraci na nový server.
5. Zadejte heslo pro účet, který aktuálně používáte k připojení ke službě Azure AD. Musí se jednat o účet, který DirSync aktuálně používá.  
   ![Zadejte svoje přihlašovací údaje služby Azure AD](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   Pokud se zobrazí chyba a máte problémy s připojením, přečtěte si téma [Řešení problémů s připojením](tshoot-connect-connectivity.md).
6. Zadejte účet správce podnikové sítě pro Active Directory.  
   ![Zadejte svoje přihlašovací údaje ke službě AD DS.](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Nyní můžete provést konfiguraci. Pokud kliknete na tlačítko **Upgradovat**, odinstaluje se nástroj DirSync a nakonfiguruje se služba Azure AD Connect a zahájí synchronizaci.  
   ![Připraveno ke konfiguraci](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Po dokončení instalace se odhlaste a znovu přihlaste do Windows. Teprve pak použijte Synchronization Service Manager, Synchronization Rule Editor, případně proveďte další změny v konfiguraci.

## <a name="parallel-deployment"></a>Paralelní nasazení
### <a name="export-the-dirsync-configuration"></a>Export konfigurace nástroje DirSync
**Paralelní nasazení s více než 50 tisíci objekty**

Pokud máte více než 50 tisíc objektů, doporučujeme při instalaci Azure AD Connect použít paralelní nasazení.

Zobrazí se obrazovka, která se podobá této:  
![Analýza dokončena](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Pokud chcete pokračovat v paralelním nasazení, je třeba provést následující kroky:

* Klikněte na tlačítko **Exportovat nastavení**. Pokud Azure AD Connect instalujete na samostatný server, budou tato nastavení migrována z vašeho stávajícího nástroje DirSync do nové instalace služby Azure AD Connect.

Po dokončení exportu nastavení můžete na serveru nástroje DirSync ukončit průvodce služby Azure AD Connect. Pokračujte dalším krokem a [nainstalujte Azure AD Connect na samostatný server.](#installation-of-azure-ad-connect-on-separate-server)

**Paralelní nasazení s méně než 50 tisíci objekty**

Pokud máte méně než 50 tisíc objektů, ale přesto chcete provést paralelní nasazení, postupujte následovně:

1. Spusťte instalační program služby Azure AD Connect (MSI).
2. Až se zobrazí obrazovka **Vítá vás Azure AD Connect**, ukončete průvodce instalací kliknutím na křížek v pravém horním rohu okna.
3. Otevřete příkazový řádek.
4. Z umístění instalace služby Azure AD Connect (výchozí: C:\Program Files\Microsoft Azure Active Directory Connect) spusťte následující příkaz: `AzureADConnect.exe /ForceExport`.
5. Klikněte na tlačítko **Exportovat nastavení**. Pokud Azure AD Connect instalujete na samostatný server, budou tato nastavení migrována z vašeho stávajícího nástroje DirSync do nové instalace služby Azure AD Connect.

![Analýza dokončena](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

Po dokončení exportu nastavení můžete na serveru nástroje DirSync ukončit průvodce služby Azure AD Connect. Pokračujte dalším krokem a [nainstalujte Azure AD Connect na samostatný server](#installation-of-azure-ad-connect-on-separate-server).

### <a name="install-azure-ad-connect-on-separate-server"></a>Instalace služby Azure AD Connect na samostatný server
Při instalaci služby Azure AD Connect na nový server bude instalační program předpokládat, že chcete provést čistou instalaci Azure AD Connect. Protože ale chcete použít konfiguraci nástroje DirSync, proveďte několik kroků navíc:

1. Spusťte instalační program služby Azure AD Connect (MSI).
2. Až se zobrazí obrazovka **Vítá vás Azure AD Connect**, ukončete průvodce instalací kliknutím na křížek v pravém horním rohu okna.
3. Otevřete příkazový řádek.
4. Z umístění instalace služby Azure AD Connect (výchozí: C:\Program Files\Microsoft Azure Active Directory Connect) spusťte následující příkaz: `AzureADConnect.exe /migrate`.
   Spustí se průvodce instalací služby Azure AD Connect a nabídne vám následující obrazovku:  
   ![Zadejte svoje přihlašovací údaje služby Azure AD](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. Vyberte soubor s nastavením exportovaný z instalace nástroje DirSync.
6. Nakonfigurujte rozšířené možnosti, například:
   * Vlastní umístění instalace pro Azure AD Connect.
   * Existující instanci systému SQL Server (výchozí: Azure AD Connect nainstaluje systém SQL Server 2012 Express). Nepoužívejte stejnou instanci databáze jako server nástroje DirSync.
   * Účet služby, který používáte k připojení k systému SQL Server (pokud je vaše databáze v systému SQL Server vzdálená, pak tento účet musí být účtem doménové služby).
     Tyto možnosti se zobrazí na této obrazovce:  
     ![Zadejte svoje přihlašovací údaje služby Azure AD](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. Klikněte na **Další**.
8. Na stránce **Připraveno ke konfiguraci** nechejte zaškrtnuté políčko **Zahájit proces synchronizace ihned po dokončení konfigurace**. Server je teď v [pracovním režimu](how-to-connect-sync-operations.md#staging-mode), takže změny se nebudou exportovat do služby Azure AD.
9. Klikněte na **Nainstalovat**.
10. Po dokončení instalace se odhlaste a znovu přihlaste do Windows. Teprve pak použijte Synchronization Service Manager, Synchronization Rule Editor, případně proveďte další změny v konfiguraci.

> [!NOTE]
> Bude zahájena synchronizace mezi službami Windows Server Active Directory a Azure Active Directory, ale žádné změny se nebudou exportovat do služby Azure AD. V jednu chvíli může změny aktivně exportovat pouze jeden synchronizační nástroj. Tento stav se nazývá [pracovní režim](how-to-connect-sync-operations.md#staging-mode).

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Kontrola připravenosti služby Azure AD Connect k zahájení synchronizace
Pro ověření, že je služba Azure AD Connect připravena převzít roli nástroje DirSync, je třeba pomocí nabídky Start otevřít **Synchronization Service Manager** ve skupině **Azure AD Connect**.

V aplikaci přejděte na kartu **Operace**. Na této kartě uvidíte potvrzení, zda byly provedeny následující operace:

* Import v konektoru služby AD
* Import v konektoru služby Azure AD
* Plná synchronizace v konektoru služby AD
* Plná synchronizace v konektoru služby Azure AD

![Import a synchronizace dokončeny](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

Zkontrolujte výsledky těchto operací a ujistěte se, že nikde nejsou žádné chyby.

Pokud chcete zobrazit a zkontrolovat změny, které mají být exportovány do služby Azure AD, přečtěte si postup ověření konfigurace v [pracovním režimu](how-to-connect-sync-operations.md#staging-mode). Proveďte požadované změny, pokud nenarazíte na nic neočekávaného.

Pokud jste provedli tyto kroky a jste spokojeni s výsledkem, jste připraveni na přepnutí z nástroje DirSync na Azure AD.

### <a name="uninstall-dirsync-old-server"></a>Odinstalace nástroje DirSync (starý server)
* V nabídce **Programy a funkce** vyhledejte **synchronizační nástroj služby Windows Azure Active Directory**.
* Odinstalujte **synchronizační nástroj služby Windows Azure Active Directory**.
* Odinstalace může trvat až 15 minut.

Pokud budete chtít odinstalovat DirSync později, můžete také dočasně vypnout server nebo zakázat službu. Pokud dojde k nějaké chybě, budete ho moct znovu povolit. Neočekává se však, že by následující krok mohl selhat, takže by tento postup neměl být potřeba.

Když DirSync odinstalujete nebo vypnete, nebude už k dispozici žádný aktivní server exportující do služby Azure AD. Následující krok, povolení služby Azure AD Connect, je třeba provést dřív, než budou jakékoli změny v místní službě Active Directory dále synchronizovány se službou Azure AD.

### <a name="enable-azure-ad-connect-new-server"></a>Povolení služby Azure AD Connect (nový server)
Když po instalaci znovu otevřete službu Azure AD Connect, budete moci provést další změny v konfiguraci. Z nabídky Start nebo pomocí zástupce na ploše spusťte **Azure AD Connect**. Zkontrolujte, jestli se nepokoušíte znovu spustit instalační soubor MSI.

Měli byste vidět následující:  
![Další úlohy](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* Vyberte **Konfigurovat pracovní režim**.
* Zrušte zaškrtnutí políčka **Pracovní režim povolen**, a vypněte tak pracovní režim.

![Zadejte svoje přihlašovací údaje služby Azure AD.](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* Klikněte na tlačítko **Další**.
* Na potvrzovací stránce klikněte na tlačítko **Instalovat**.

Azure AD Connect je nyní váš aktivní server a už nemůžete přejít zpět na stávající server DirSync.

## <a name="next-steps"></a>Další postup
Nyní, když máte nainstalovanou službu Azure AD Connect, můžete si [ověřit instalaci a přiřadit licence](how-to-connect-post-installation.md).

Další informace o těchto nových funkcích, které byly povoleny v rámci instalace: [Automatický upgrade](how-to-connect-install-automatic-upgrade.md), [prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md), a [Azure AD Connect Health](how-to-connect-health-sync.md).

Zjistěte více o těchto běžných tématech: [plánovač a jak aktivovat synchronizaci](how-to-connect-sync-feature-scheduler.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
