---
title: Poradce při potížích s připojením – Hyperscale (Citus) – databáze Azure pro PostgreSQL
description: Zjistěte, jak řešit problémy s připojením k Azure Database for PostgreSQL – Hyperscale (Citus)
keywords: postgresql připojení,připojovací řetězec,problémy s připojením,přechodná chyba,chyba připojení
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977501"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Řešení problémů s připojením k databázi Azure pro PostgreSQL – hyperškálování (Citus)

Problémy s připojením mohou být způsobeny několika věcmi, například:

* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací informace
* Pro skupinu serverů bylo dosaženo limitu připojení.
* Problémy s infrastrukturou služby
* Servisní údržba
* Převzetí selhání uzlu koordinátora na nový hardware

Obecně platí, že problémy s připojením k hyperškálování lze klasifikovat takto:

* Přechodné chyby (krátkodobé nebo přerušované)
* Trvalé nebo nepřechodné chyby (chyby, které se pravidelně opakují)

## <a name="troubleshoot-transient-errors"></a>Poradce při potížích s přechodným chybám

K přechodným chybám dochází z mnoha důvodů. Mezi nejběžnější patří údržba systému, chyba s hardwarem nebo softwarem a upgrady virtuálních jader uzlu koordinátora.

Povolení vysoké dostupnosti pro uzly skupiny serverů Hyperscale může tyto typy problémů automaticky zmírnit. Aplikace by však měla být stále připravena krátce ztratit připojení. Také jiné události může trvat déle zmírnit, například když velké transakce způsobí dlouhotrvající obnovení.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Postup řešení problémů s přechodným připojením

1. Zkontrolujte [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadky, ke kterým došlo během doby, ve které aplikace hlásila chyby.
2. Aplikace, které se připojují ke cloudové službě, jako je Hyperscale (Citus), by měly očekávat přechodné chyby a řádně reagovat. Například aplikace by měly implementovat logiku opakování pro zpracování těchto chyb namísto jejich navařování jako chyby aplikace pro uživatele.
3. Jakmile se skupina serverů blíží omezením prostředků, mohou se chyby jevit jako přechodné problémy s připojením. Zvýšení paměti RAM uzlu nebo přidání pracovních uzlů a vyvažování dat může pomoci.
4. Pokud problémy s připojením přetrvávají nebo trvají déle než 60 sekund nebo se stávají více než jednou denně, zadejte žádost o podporu Azure výběrem **možnosti Získat podporu** na webu [podpory Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Poradce při potížích s trvalými chybami

Pokud se aplikaci trvale nedaří připojit k hyperscale (Citus), nejčastější příčinou jsou chybná konfigurace brány firewall nebo chyba uživatele.

* Konfigurace brány firewall uzlu koordinátora: Ujistěte se, že je brána firewall serveru Hyperscale nakonfigurována tak, aby umožňovala připojení z vašeho klienta, včetně proxy serverů a bran.
* Konfigurace brány firewall klienta: Brána firewall klienta musí umožňovat připojení k databázovému serveru. Některé brány firewall vyžadují povolení nejen aplikace podle názvu, ale povolení IP adres a portů serveru.
* Chyba uživatele: Zkontrolujte připojovací řetězec. Pravděpodobně jste zadali chybně zadané parametry, jako je název serveru. Připojovací řetězce pro různé jazykové architektury a psql najdete na webu Azure Portal. Přejděte na stránku **Připojovací řetězce** ve skupině serverů Hyperscale (Citus). Také mějte na paměti, že Hyperscale (Citus) clustery mají pouze jednu databázi a jeho předdefinovaný název je **citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Postup řešení přetrvávajících problémů s připojením

1. Nastavte [pravidla brány firewall](howto-hyperscale-manage-firewall-using-portal.md) tak, aby umožňovala IP adresu klienta. Pouze pro dočasné testovací účely nastavte pravidlo brány firewall, které používá 0.0.0.0 jako počáteční adresu IP a jako koncovou adresu IP 255.255.255. Toto pravidlo otevře server všem IP adresám. Pokud pravidlo vyřeší problém s připojením, odeberte ho a vytvořte pravidlo brány firewall pro přiměřeně omezenou adresu IP nebo rozsah adres.
2. U všech bran firewall mezi klientem a Internetem zkontrolujte, zda je port 5432 otevřený pro odchozí připojení.
3. Ověřte připojovací řetězec a další nastavení připojení.
4. Zkontrolujte stav služby na řídicím panelu.

## <a name="next-steps"></a>Další kroky

* Naučte se koncepty [pravidel brány firewall v Databázi Azure pro PostgreSQL – Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md)
* Podívejte se, jak [spravovat pravidla brány firewall pro Azure Database for PostgreSQL – Hyperscale (Citus)](howto-hyperscale-manage-firewall-using-portal.md)
