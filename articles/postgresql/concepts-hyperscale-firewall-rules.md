---
title: Pravidla brány firewall – Citus (-Scale) – Azure Database for PostgreSQL
description: Tento článek popisuje pravidla brány firewall pro Azure Database for PostgreSQL – Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 559c5eca6fa8a6eceb37ade003d4f1983c0a1a1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90902083"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Pravidla brány firewall v Azure Database for PostgreSQL – Citus (škálování)
Brána firewall serveru Azure Database for PostgreSQL zabraňuje všem přístupům k uzlu koordinátora Citus), dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru na základě zdrojové IP adresy jednotlivých požadavků.
Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k uzlu koordinátora Citus (), tedy ke všem databázím v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru se dají nakonfigurovat pomocí Azure Portal. Chcete-li vytvořit pravidla brány firewall na úrovni serveru, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Všechna přístupová práva k vašemu uzlu koordinátora jsou ve výchozím nastavení blokována branou firewall. Pokud chcete začít používat server z jiného počítače, musíte zadat jedno nebo více pravidel brány firewall na úrovni serveru, abyste mohli povolit přístup k vašemu serveru. Pomocí pravidel brány firewall určete, které rozsahy IP adres z Internetu mají být povoleny. Přístup k samotnému webu Azure Portal nemá vliv na pravidla brány firewall.
Pokusy o připojení z Internetu a Azure musí nejdřív projít přes bránu firewall, aby se mohli připojit k databázi PostgreSQL, jak je znázorněno v následujícím diagramu:

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="Příklad toku fungování brány firewall":::

## <a name="connecting-from-the-internet-and-from-azure"></a>Připojení z Internetu a z Azure

Brána firewall skupiny serverů Citus () určuje, kdo se může připojit k uzlu koordinátora skupiny. Brána firewall určí přístup tím, že se pojednáním s konfigurovatelným seznamem pravidel. Každé pravidlo je IP adresa nebo rozsah adres, které jsou povoleny v.

Když brána firewall blokuje připojení, může způsobit chyby aplikace. Použití ovladače PostgreSQL JDBC například vyvolá chybu podobnou této:

> java.util.concurrent.ExecutionException: Java. lang. RuntimeException –: org. PostgreSQL. util. PSQLException: ZÁVAŽNá: No pg \_ HBA. conf pro hostitele "123.45.67.890", "citus", Database "citus", SSL

Informace o tom, jak jsou pravidla definovaná, najdete v tématu [Vytvoření a Správa pravidel brány firewall](howto-hyperscale-manage-firewall-using-portal.md) .

## <a name="troubleshooting-the-database-server-firewall"></a>Řešení potíží s bránou firewall databázového serveru
Když se přístup k Microsoft Azure databázi pro službu PostgreSQL-Citus () nechová podle očekávání, zvažte tyto body:

* **Změny v seznamu povolených se zatím neprojevily:** Může trvat až pět minut, než se změny konfigurace brány firewall Citus () projeví.

* **Uživatel nemá oprávnění nebo se použilo nesprávné heslo:** Pokud uživatel nemá na serveru oprávnění nebo je použité heslo nesprávné, připojení k serveru je odepřené. Při vytváření nastavení brány firewall jsou jenom klienti s příležitostí k pokusu o připojení k vašemu serveru. Každý klient musí stále zadat potřebná zabezpečovací pověření.

Například pomocí klienta JDBC se může zobrazit následující chyba.
> java.util.concurrent.ExecutionException: Java. lang. RuntimeException –: org. PostgreSQL. util. PSQLException: ZÁVAŽNá: ověřování heslem pro uživatele "uživatelské_jméno" se nezdařilo.

* **Dynamická IP adresa:** Pokud vaše internetové připojení používá dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jedno z následujících řešení:

* Zeptejte se poskytovatele internetových služeb (ISP) na rozsah IP adres přiřazený ke klientským počítačům, které přistupují k uzlu koordinátora Citus (webscale), a pak přidejte rozsah IP adres jako pravidlo brány firewall.

* Místo toho Získejte statické IP adresy pro klientské počítače a pak přidejte statickou IP adresu jako pravidlo brány firewall.

## <a name="next-steps"></a>Další kroky
Články o vytváření pravidel brány firewall na úrovni serveru a databáze najdete v těchto tématech:
* [Vytváření a Správa Azure Database for PostgreSQL pravidel brány firewall pomocí Azure Portal](howto-hyperscale-manage-firewall-using-portal.md)
