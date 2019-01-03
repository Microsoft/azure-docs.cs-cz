---
title: Azure Database for pravidla firewallu serveru MySQL
description: Popisuje pravidla brány firewall pro váš server Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a7016b8ca43abee9c3f346c6dec55a101ce4020a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541213"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database for pravidla firewallu serveru MySQL
Že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru, na základě původní IP adresy každé žádosti.

Pokud chcete nakonfigurovat bránu firewall, vytvořte pravidla brány firewall určující rozsahy přípustných IP adres. Můžete vytvořit pravidla brány firewall na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k vaší celý server Azure Database for MySQL, to znamená, že všechny databáze v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru můžete konfigurovat pomocí webu Azure portal nebo příkazy rozhraní příkazového řádku Azure. K vytvoření pravidla brány firewall na úrovni serveru, musíte být vlastníkem nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Všechny databáze přístupu k vaší databázi Azure pro MySQL server je ve výchozím nastavení blokován branou firewall. Pokud chcete začít používat svůj server z jiného počítače, musíte zadat jedno nebo více pravidel brány firewall na úrovni serveru umožňuje přístup k vašemu serveru. Použití pravidel brány firewall k určení IP Adrese rozsahy adres z Internetu, a povolit. Přístup k webu Azure portal, samotný není ovlivněn pravidla brány firewall.

Pokusy o připojení z Internetu a z Azure musí nejdříve projít přes bránu firewall předtím, než mají přístup k vaší databázi Azure Database for MySQL, jak je znázorněno v následujícím diagramu:

![Příklad postupu fungování brány firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru platí pro všechny databáze v Azure Database for MySQL server.

Pokud IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení je povoleno.

Pokud IP adresa požadavku je mimo žádném rozsahů určených v pravidlech brány firewall na úrovni databáze nebo serveru, pak požadavek na připojení selže.

## <a name="connecting-from-azure"></a>Připojení z Azure
Pokud chcete umožnit aplikacím z Azure připojení k Azure Database for MySQL server, musí být povolená připojení Azure. Například pro hostování aplikace Azure Web Apps nebo aplikace, která běží ve Virtuálním počítači Azure nebo k připojení ze Brána pro správu dat Azure Data Factory. Prostředky se nemusíte být ve stejné virtuální síti (VNet) nebo skupinu prostředků pro pravidlo brány firewall, aby tato připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které chcete povolit tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete nastavit **povolit přístup ke službám Azure** umožňuje **ON** portálu ze **zabezpečení připojení** podokna a stiskněte klávesu **Uložit**. Pokud pokus o připojení není povolený, požadavek vůbec nedostane serveru Azure Database for MySQL.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

![Povolit přístup ke službám Azure konfigurovat na portálu](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě webu Azure portal můžete pravidla brány firewall prostřednictvím kódu programu spravovat pomocí rozhraní příkazového řádku Azure. Viz také [vytvořit a spravovat Azure Database for MySQL pravidel brány firewall pomocí Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Řešení potíží s branou firewall databáze
Při přístupu k databázi Microsoft Azure pro MySQL server služby se nechová podle očekávání, zvažte následující body:

* **Změny do seznamu povolených ještě nevstoupilo v platnost:** Může být co nejvíce pět minut, než se změny k Azure Database for MySQL Server firewall konfigurace se projeví.

* **Přihlášení není autorizováno nebo bylo nesprávné heslo:** Pokud přihlášení nemá oprávnění serveru Azure Database for MySQL nebo použít heslo není správné, připojení k Azure Database for MySQL serveru byl odepřen. Vytvoření nastavení brány firewall klientům pouze poskytuje možnost pokusit se o připojení k vašemu serveru – každý klient musí dodat potřebné zabezpečené přihlašovací údaje.

* **Dynamická IP adresa:** Pokud máte připojení k Internetu dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jednu z následujících řešení:

* Požádejte poskytovatele služeb Internetu (ISP) o rozsah IP adres přidělený vašim klientským počítačům, které přistupují k Azure Database for MySQL serveru a pak přidat rozsah IP adres jako pravidlo brány firewall.

* Získejte pro své klientské počítače statické přidělování IP adres a následně přidejte tyto IP adresy jako pravidla brány firewall.

## <a name="next-steps"></a>Další postup

[Vytvoření a správě Azure Database for MySQL – pravidla brány firewall pomocí webu Azure portal](./howto-manage-firewall-using-portal.md)
[vytvořit a spravovat Azure Database for MySQL pravidel brány firewall pomocí Azure CLI](./howto-manage-firewall-using-cli.md)
