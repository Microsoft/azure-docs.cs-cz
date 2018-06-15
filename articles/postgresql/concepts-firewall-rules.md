---
title: Databáze Azure pro pravidla brány firewall serveru PostgreSQL
description: Tento článek popisuje pravidla brány firewall pro vaši databázi Azure pro PostgreSQL server.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 8a3f5d9fa8f1c36d8468c38f7dda803d3ca1d832
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29689883"
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Databáze Azure pro pravidla brány firewall serveru PostgreSQL
Azure databázi PostgreSQL serverová brána firewall brání veškerý přístup k databázovému serveru, dokud je určit, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru na základě původní IP adresy každého požadavku.
Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Můžete vytvořit pravidla brány firewall na úrovni serveru.

**Pravidla brány firewall:** tato pravidla povolit klientům přístup k celé databáze Azure pro PostgreSQL Server, to znamená, všechny databáze v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru se dá nakonfigurovat pomocí portálu Azure nebo pomocí rozhraní příkazového řádku Azure. Pokud chcete vytvořit pravidla brány firewall na úrovni serveru, musí být vlastník předplatného nebo jeho přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Všechny databáze přístup k vaší databázi Azure PostgreSQL serveru je blokován branou firewall ve výchozím nastavení. Pokud chcete začít používat server z jiného počítače, je třeba zadat jeden nebo více pravidel brány firewall na úrovni serveru pro povolení přístupu k serveru. Použití pravidel brány firewall můžete určit IP rozsahy adres z Internetu, a povolit. Přístup k webu portálu Azure, samotné nebude ovlivněné pravidla brány firewall.
Pokusy o připojení z Internetu a Azure musí nejdřív projít přes bránu firewall než dosáhnou databáze PostgreSQL, jak je znázorněno v následujícím diagramu:

![Příklad toku fungování brány firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru platí pro všechny databáze na databázi Azure pro PostgreSQL server. Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení je povoleno.
Pokud IP adresa požadavku není v rámci rozsahů určených v žádné z pravidel brány firewall na úrovni serveru, požadavek na připojení se nezdaří.
Například pokud vaše aplikace připojí k ovladač JDBC pro PostgreSQL, se může zobrazit tato chyba při pokusu připojit se, když brána firewall blokuje připojení.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: závažná: žádné pg\_hba.conf položka pro uživatele "adminuser" hostitel "123.45.67.890", databáze "postgresql", SSL

## <a name="connecting-from-azure"></a>Připojení z Azure
Povolit aplikacím z Azure se připojit k vaší databázi Azure pro PostgreSQL server, musí být povoleno Azure připojení. Například k hostování aplikace Azure Web Apps nebo aplikaci, která běží ve virtuálním počítači Azure nebo pro připojení z brány správy dat Azure Data Factory. Prostředky se nemusíte být ve stejné virtuální síti (VNet) nebo skupinu prostředků pro pravidlo brány firewall povolit těchto připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které chcete povolit tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete nastavit **povolit přístup ke službám Azure** možnost k **ON** na portálu ze **zabezpečení připojení** podokně a stiskněte klávesu **Uložit**. Pokud pokus o připojení není povolena, požadavek nebylo dosaženo databáze Azure pro PostgreSQL server.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

![Povolit přístup ke službám Azure konfigurovat na portálu](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě portálu Azure lze spravovat pravidla brány firewall programově pomocí rozhraní příkazového řádku Azure.
Viz také [vytvořit a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí rozhraní příkazového řádku Azure](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-server-firewall"></a>Řešení potíží s brány firewall serveru databáze
Pokud přístup k databázi Microsoft Azure pro službu PostgreSQL Server tak, jak očekáváte, vezměte v úvahu následující body:

* **Změny v seznamu povolených ještě nevstoupilo v platnost:** může být co nejvíce pět minut zpoždění pro změny databáze Azure pro konfiguraci brány firewall serveru PostgreSQL vstoupily v platnost.

* **Přihlášení nemá oprávnění nebo nesprávné heslo byl použit:** Pokud přihlášení nemá oprávnění v databázi Azure pro PostgreSQL server nebo je chybné heslo použít, připojení k databázi Azure PostgreSQL serveru byl odepřen. Vytvoření nastavení brány firewall pouze nabízí klientům s příležitost k pokusu o připojení k serveru. Každý klient musí stále zadejte potřebná zabezpečovací pověření.

Například pomocí klienta JDBC, může objevit následující chyba.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: závažná: selhalo ověřování hesla pro uživatele "uživatelské_jméno"

* **Dynamická IP adresa:** Pokud vaše internetové připojení používá dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jedno z následujících řešení:

* Požádejte poskytovatele služeb Internetu (ISP) pro rozsah IP adres přiřazené v klientských počítačích, které přístup k databázi Azure pro PostgreSQL Server a pak přidat rozsah IP adres jako pravidlo brány firewall.

* Získat statické IP adresy místo pro klientské počítače a poté přidejte statickou IP adresu jako pravidlo brány firewall.

## <a name="next-steps"></a>Další postup
Články o vytvoření pravidla brány firewall serveru úroveň a databáze naleznete na stránce:
* [Vytvářet a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí portálu Azure](howto-manage-firewall-using-portal.md)
* [Vytvářet a spravovat databáze Azure pro pravidla brány firewall PostgreSQL pomocí rozhraní příkazového řádku Azure](howto-manage-firewall-using-cli.md)
