---
title: Azure Database for pravidla brány firewall na serveru PostgreSQL
description: Tento článek popisuje pravidla brány firewall pro váš server Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 793a68ee829d87a8433d5bdd77fe7b43e2f3af19
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548829"
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Azure Database for pravidla brány firewall na serveru PostgreSQL
Azure Database for PostgreSQL Server brány firewall brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru, na základě původní IP adresy každé žádosti.
Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Můžete vytvořit pravidla brány firewall na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k celou databázi Azure PostgreSQL server, to znamená, že všechny databáze v rámci stejného logického serveru. Pomocí webu Azure portal nebo pomocí příkazů Azure CLI je možné nakonfigurovat pravidla brány firewall na úrovni serveru. K vytvoření pravidla brány firewall na úrovni serveru, musíte být vlastníkem nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Databáze přístup k vaší databázi Azure pro PostgreSQL server je blokován branou firewall ve výchozím nastavení. Pokud chcete začít používat svůj server z jiného počítače, musíte zadat jedno nebo více pravidel brány firewall na úrovni serveru umožňuje přístup k vašemu serveru. Použití pravidel brány firewall k určení IP Adrese rozsahy adres z Internetu, a povolit. Přístup k webu Azure portal, samotný není ovlivněn pravidla brány firewall.
Pokusy o připojení z Internetu a z Azure musí nejprve projít přes bránu firewall předtím, než mají přístup k databázi PostgreSQL, jak je znázorněno v následujícím diagramu:

![Příklad postupu fungování brány firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru platí pro všechny databáze na stejném serveru Azure Database for PostgreSQL. Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení je povoleno.
Pokud IP adresa požadavku není v rámci žádném rozsahů určených v pravidlech brány firewall na úrovni serveru, požadavek na připojení selže.
Například pokud vaše aplikace připojuje se ovladač JDBC pro PostgreSQL, může dojít k této chybě při pokusu připojit se, když brána firewall blokuje připojení.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: Závažná chyba: žádné pg\_hba.conf záznam pro hostitele "123.45.67.890" uživatel "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Připojení z Azure
Pokud chcete umožnit aplikacím z Azure připojení k Azure Database for PostgreSQL server, musí být povolená připojení Azure. Například pro hostování aplikace Azure Web Apps nebo aplikace, která běží ve Virtuálním počítači Azure nebo k připojení ze Brána pro správu dat Azure Data Factory. Prostředky se nemusíte být ve stejné virtuální síti (VNet) nebo skupinu prostředků pro pravidlo brány firewall, aby tato připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které chcete povolit tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete nastavit **povolit přístup ke službám Azure** umožňuje **ON** portálu ze **zabezpečení připojení** podokna a stiskněte klávesu **Uložit**. Pokud pokus o připojení není povolený, požadavek vůbec nedostane serveru Azure Database for PostgreSQL.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

![Povolit přístup ke službám Azure konfigurovat na portálu](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě webu Azure portal je možné pravidla brány firewall spravovat programově pomocí rozhraní příkazového řádku Azure.
Viz také [vytvořit a spravovat Azure Database for postgresql – pravidla brány firewall pomocí Azure CLI](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-server-firewall"></a>Řešení potíží s branou firewall pro server databáze
Pokud přístup k databázi Microsoft Azure pro službu PostgreSQL Server nechová podle očekávání, zvažte následující body:

* **Změny do seznamu povolených ještě nevstoupilo v platnost:** Může být co nejvíce pět minut, než se změny k Azure Database for PostgreSQL Server konfigurace brány firewall projeví.

* **Přihlášení není autorizováno nebo bylo nesprávné heslo:** Pokud přihlášení nemá oprávnění serveru Azure Database for PostgreSQL nebo použít heslo není správné, připojení k Azure Database for PostgreSQL serveru byl odepřen. Vytvoření nastavení brány firewall pouze poskytuje klientům možnost pokusit se o připojení k serveru. Každý klient musí stále dodat potřebné zabezpečené přihlašovací údaje.

Například pomocí klienta JDBC, může objevit následující chyba.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: Závažná chyba: nepovedlo se ověřování pomocí hesla pro uživatele "uživatelské_jméno"

* **Dynamická IP adresa:** Pokud máte připojení k Internetu dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jedno z následujících řešení:

* Požádejte poskytovatele služeb Internetu (ISP) o rozsah IP adres přidělený vašim klientským počítačům, které přistupují k Azure Database for PostgreSQL Server a pak přidat rozsah IP adres jako pravidlo brány firewall.

* Získejte statické přidělování IP adres pro klientské počítače a pak přidat statická IP adresa jako pravidlo brány firewall.

## <a name="next-steps"></a>Další postup
Články o vytvoření pravidla brány firewall na úrovni serveru a databáze naleznete v tématu:
* [Vytvoření a správě Azure Database for postgresql – pravidla brány firewall pomocí webu Azure portal](howto-manage-firewall-using-portal.md)
* [Vytvoření a správě Azure Database for postgresql – pravidla brány firewall pomocí Azure CLI](howto-manage-firewall-using-cli.md)
