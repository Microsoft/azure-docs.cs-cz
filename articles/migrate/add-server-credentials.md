---
title: Zadání přihlašovacích údajů serveru pro zjišťování aplikací, závislostí a instancí SQL Server a databází
description: Přečtěte si, jak zadat přihlašovací údaje serveru pro Správce konfigurace zařízení.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 2359855ce3949eb022a03f6e8e2dbc05f98907db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054595"
---
# <a name="provide-server-credentials-to-discover-applications-dependencies-and-sql-server-instances-and-databases"></a>Zadání přihlašovacích údajů serveru pro zjišťování aplikací, závislostí a instancí SQL Server a databází

V tomto článku se dozvíte, jak přidat více přihlašovacích údajů serveru do Správce konfigurace zařízení k provádění inventáře softwaru (zjišťování nainstalovaných aplikací), analýzy závislostí bez agentů a zjišťování SQL Server instancí a databází.

> [!Note]
> Zjišťování a hodnocení instancí SQL Server a databází spuštěných ve vašem prostředí VMware je teď ve verzi Preview. Pokud chcete tuto funkci vyzkoušet, použijte [**tento odkaz**](https://aka.ms/AzureMigrate/SQL) a vytvořte projekt v oblasti **Austrálie – východ**. Pokud již máte projekt v oblasti Austrálie – východ a chcete tuto funkci vyzkoušet, na portálu se ujistěte, že jste splnili tyto [**požadavky**](how-to-discover-sql-existing-project.md).

[Zařízení Azure Migrate](migrate-appliance.md) je jednoduché zařízení, které používá Azure Migrate: posouzení serveru pro zjišťování místních serverů běžících v prostředí VMware a posílání metadat konfigurace serveru a výkonu do Azure. Zařízení se taky dá použít k provádění inventáře softwaru, analýze závislostí bez agentů a zjišťování instancí SQL Server a databází.

Pokud chcete tyto funkce využít, můžete zadat přihlašovací údaje serveru podle následujících kroků. Zařízení se pokusí automaticky mapovat přihlašovací údaje na servery, aby se prováděly funkce zjišťování.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Přidání přihlašovacích údajů pro servery běžící v prostředí VMware

### <a name="types-of-server-credentials-supported"></a>Typy přihlašovacích údajů serveru jsou podporované.

Do Správce konfigurace zařízení můžete přidat několik přihlašovacích údajů serveru, které můžou být domény, nedoménová (Windows nebo Linux) nebo SQL Server přihlašovací údaje pro ověřování.

Typy přihlašovacích údajů serveru, které jsou podporované, jsou uvedené v následující tabulce:

Typ přihlašovacích údajů | Description
--- | ---
**Přihlašovací údaje domény** | **Přihlašovací údaje domény** můžete přidat tak, že vyberete možnost z rozevírací nabídky v modálním seznamu **Přidat pověření** . <br/><br/> Pokud chcete zadat přihlašovací údaje k doméně, musíte zadat **název domény** , který musí být zadaný ve formátu plně kvalifikovaného názvu domény (např. prod.corp.contoso.com). <br/><br/> Také je nutné zadat popisný název pro přihlašovací údaje, uživatelské jméno a heslo. <br/><br/> Přidaná pověření domény budou automaticky ověřena pro pravost proti službě Active Directory domény. K tomu je potřeba zabránit jakémukoli uzamčení účtu, když se zařízení pokusí mapovat přihlašovací údaje domény proti zjištěným serverům. <br/><br/> Zařízení se nebude pokoušet mapovat přihlašovací údaje domény, jejichž ověření se nezdařilo. Aby bylo možné pokračovat v inventarizaci softwaru, musíte mít aspoň jedno úspěšně ověřené přihlašovací údaje do domény nebo aspoň jedno nedoménové přihlašovací údaje.<br/><br/>Přihlašovací údaje domény mapované na servery Windows se budou používat k provádění inventáře softwaru a dají se také použít ke zjišťování instancí a databází SQL Server _(Pokud jste v SQL serverech nakonfigurovali režim ověřování systému Windows)_.<br/> [Přečtěte si další informace](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) o typech režimů ověřování podporovaných na serverech SQL.
**Přihlašovací údaje jiného typu než doména (Windows/Linux)** | Pokud chcete přidat **Windows (jiný systém než doménu)** nebo **Linux (nedoménová)** , vyberte v rozevíracím seznamu v modálním políčku **Přidat pověření** možnost požadovaná možnost. <br/><br/> Je nutné zadat popisný název pro přihlašovací údaje, uživatelské jméno a heslo.
**SQL Server přihlašovací údaje pro ověřování** | Přihlašovací údaje pro **ověření SQL Server** můžete přidat tak, že vyberete možnost z rozevírací nabídky v modální části **Přidat pověření** . <br/><br/> Je nutné zadat popisný název pro přihlašovací údaje, uživatelské jméno a heslo. <br/><br/> Tento typ přihlašovacích údajů můžete přidat, pokud chcete zjišťovat SQL Server instance a databáze běžící v prostředí VMware, pokud jste nakonfigurovali SQL Server režim ověřování na SQL serverech.<br/> [Přečtěte si další informace](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) o typech režimů ověřování podporovaných na serverech SQL.<br/><br/> Musíte zadat aspoň jedno úspěšně ověřené přihlašovací údaje do domény nebo aspoň jedno přihlašovací údaje pro Windows (mimo doménu), aby zařízení mohlo dokončit inventář softwaru, aby bylo možné zjistit, jestli je SQL Server nainstalovaný na serverech, než použije přihlašovací údaje pro SQL Server pro zjišťování SQL Server instancí a databází.

Ověřte oprávnění požadovaná v přihlašovacích údajích pro Windows/Linux, abyste mohli provádět inventář softwaru, analýzu závislostí bez agentů a zjišťovat SQL Server instance a databáze.

### <a name="required-permissions"></a>Požadovaná oprávnění

Následující tabulka uvádí oprávnění požadovaná pro přihlašovací údaje serveru, které jsou k dispozici na zařízení, aby bylo možné provádět příslušné funkce:

Funkce | přihlašovací údaje pro Windows. | Přihlašovací údaje pro Linux
---| ---| ---
**Inventář softwaru** | Uživatelský účet hosta | Běžný/normální uživatelský účet (přístupová oprávnění, která nejsou sudo)
**Zjišťování instancí SQL Server a databází** | Uživatelský účet, který je členem role serveru sysadmin. | _Aktuálně není podporováno_
**Analýza závislostí bez agentů** | Účet domény nebo mimo doménu (místní) s oprávněními správce | Kořenový uživatelský účet nebo <br/> účet s těmito oprávněními pro soubory/bin/netstat a/bin/ls: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.<br/><br/> Tyto možnosti nastavte pomocí následujících příkazů: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/LS<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Doporučené postupy pro poskytování přihlašovacích údajů

- Doporučuje se vytvořit vyhrazený uživatelský účet domény s [požadovanými oprávněními](add-server-credentials.md#required-permissions), která je vymezená pro provádění inventáře softwaru, analýzu závislostí bez agentů a zjišťování SQL Server instancí a databází na požadovaných serverech.
- Pro zahájení inventáře softwaru doporučujeme zadat aspoň jedno úspěšně ověřené přihlašovací údaje do domény nebo aspoň jedno nedoménové přihlašovací údaje.
- Pokud chcete zjistit SQL Server instance a databáze, můžete zadat přihlašovací údaje domény, pokud jste na SQL serverech nakonfigurovali režim ověřování systému Windows.
-  Přihlašovací údaje pro ověření SQL Server můžete zadat také v případě, že jste nakonfigurovali SQL Server režim ověřování na serverech SQL, ale doporučujeme vám poskytnout aspoň jedno úspěšně ověřené přihlašovací údaje k doméně nebo aspoň jedno přihlašovací údaje pro Windows (mimo doménu), aby zařízení mohlo nejdřív dokončit inventář softwaru.

## <a name="credentials-handling-on-appliance"></a>Manipulace s přihlašovacími údaji na zařízení

- Všechna pověření poskytnutá v nástroji Configuration Manager pro zařízení jsou uložená místně na serveru zařízení a neodesílají se do Azure.
- Přihlašovací údaje uložené na serveru zařízení jsou šifrované pomocí Data Protection API (DPAPI).
- Po přidání přihlašovacích údajů se zařízení pokusí automaticky mapovat přihlašovací údaje, aby bylo možné provádět zjišťování na příslušných serverech.
- Zařízení používá přihlašovací údaje automaticky mapované na serveru pro všechny následné cykly zjišťování, do doby, než přihlašovací údaje budou moci načíst požadovaná data zjišťování. Pokud přihlašovací údaje přestanou fungovat, zařízení se znovu pokusí o mapování ze seznamu přidaných přihlašovacích údajů a pokračování probíhajícího zjišťování na serveru.
- Přidaná pověření domény budou automaticky ověřena pro pravost proti službě Active Directory domény. K tomu je potřeba zabránit jakémukoli uzamčení účtu, když se zařízení pokusí mapovat přihlašovací údaje domény proti zjištěným serverům. Zařízení se nebude pokoušet mapovat přihlašovací údaje domény, jejichž ověření se nezdařilo.
- Pokud zařízení nemůže namapovat žádná doménová nebo nedoménová pověření proti serveru, zobrazí se na serveru ve vašem projektu stav přihlašovací údaje nejsou k dispozici.

## <a name="next-steps"></a>Další kroky

Projděte si kurzy [zjišťování serverů, které běží ve vašem prostředí VMware](tutorial-discover-vmware.md) .