---
title: Pravidla brány firewall – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Tento článek popisuje pravidla brány firewall pro Azure Database for PostgreSQL – Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975563"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Pravidla brány firewall v databázi Azure pro PostgreSQL – hyperškálování (Citus)
Brána firewall serveru Azure Database for PostgreSQL zabrání veškerému přístupu k uzlu koordinátora Hyperscale (Citus), dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru na základě původní adresy IP každého požadavku.
Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k uzlu koordinátora Hyperscale (Citus), to znamená, že všechny databáze v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru lze nakonfigurovat pomocí portálu Azure. Chcete-li vytvořit pravidla brány firewall na úrovni serveru, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Veškerý přístup k databázi k uzlu koordinátora je ve výchozím nastavení blokován bránou firewall. Chcete-li začít používat server z jiného počítače, je třeba zadat jedno nebo více pravidel brány firewall na úrovni serveru, aby byl povolen přístup k serveru. Pomocí pravidel brány firewall určete, které adresy IP se pohybují od Internetu, aby je bylo možné povolit. Samotná pravidla brány firewall nemají vliv na přístup k samotnému webu portálu Azure.
Pokusy o připojení z Internetu a Azure musí nejprve projít bránou firewall, než se dostanou do vaší databáze PostgreSQL, jak je znázorněno na následujícím diagramu:

![Příklad toku fungování brány firewall](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Připojení z Internetu a z Azure

Brána firewall skupiny serverů Hyperscale (Citus) řídí, kdo se může připojit k uzlu koordinátora skupiny. Brána firewall určuje přístup nahlédnutím do konfigurovatelného seznamu pravidel. Každé pravidlo je IP adresa nebo rozsah adres, které jsou povoleny v.

Když brána firewall blokuje připojení, může způsobit chyby aplikace. Pomocí ovladače PostgreSQL JDBC, například vyvolává chybu, jako je tento:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "citus", database "citus", SSL

Informace o tom, jak jsou pravidla definována, naleznete v tématu [Vytvoření a správa pravidel brány firewall.](howto-hyperscale-manage-firewall-using-portal.md)

## <a name="troubleshooting-the-database-server-firewall"></a>Poradce při potížích s bránou firewall databázového serveru
Pokud se přístup ke službě Microsoft Azure Database for PostgreSQL – Hyperscale (Citus) nechová tak, jak očekáváte, zvažte tyto body:

* **Změny seznamu povolených položek se dosud neprojevily:** Změny konfigurace brány firewall Hyperscale (Citus) se projeví až o pět minut.

* **Uživatel není autorizován nebo bylo použito nesprávné heslo:** Pokud uživatel nemá oprávnění na serveru nebo použité heslo je nesprávné, připojení k serveru je odepřeno. Vytvoření nastavení brány firewall poskytuje klientům pouze možnost pokusit se připojit k serveru. každý klient musí stále poskytnout potřebná pověření zabezpečení.

Například pomocí klienta JDBC se může zobrazit následující chyba.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: ověřování hesla se nezdařilo pro uživatele "yourusername"

* **Dynamická IP adresa:** Pokud vaše internetové připojení používá dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jedno z následujících řešení:

* Požádejte svého poskytovatele služeb Internetu (ISP) o rozsah IP adres přiřazený klientským počítačům, které přistupují k uzlu koordinátora Hyperscale (Citus), a poté přidejte rozsah IP adres jako pravidlo brány firewall.

* Místo toho získejte statické adresování IP pro klientské počítače a přidejte statickou adresu IP jako pravidlo brány firewall.

## <a name="next-steps"></a>Další kroky
Články o vytváření pravidel brány firewall na úrovni serveru a databáze naleznete v tématu:
* [Vytváření a správa pravidel brány firewall Azure Database for PostgreSQL pomocí portálu Azure](howto-hyperscale-manage-firewall-using-portal.md)
