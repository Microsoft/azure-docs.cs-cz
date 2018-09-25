---
title: Azure Database pro MariaDB pravidla brány firewall serveru
description: Popisuje pravidla brány firewall pro váš server Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 58ccc328281bb70e00294ccef054232108212ccc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997654"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Azure Database pro MariaDB pravidla brány firewall serveru
Že brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru, na základě původní IP adresy každé žádosti.

Pokud chcete nakonfigurovat bránu firewall, vytvořte pravidla brány firewall určující rozsahy přípustných IP adres. Můžete vytvořit pravidla brány firewall na úrovni serveru.

**Pravidla brány firewall:** tato pravidla umožňují klientům přístup k vaší celý server Azure Database for MariaDB, to znamená, že všechny databáze v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru můžete konfigurovat pomocí webu Azure portal nebo příkazy rozhraní příkazového řádku Azure. K vytvoření pravidla brány firewall na úrovni serveru, musíte být vlastníkem nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Všechny databáze přístup ke službě Azure Database pro MariaDB server je ve výchozím nastavení blokován branou firewall. Pokud chcete začít používat svůj server z jiného počítače, musíte zadat jedno nebo více pravidel brány firewall na úrovni serveru umožňuje přístup k vašemu serveru. Použití pravidel brány firewall k určení IP Adrese rozsahy adres z Internetu, a povolit. Přístup k webu Azure portal, samotný není ovlivněn pravidla brány firewall.

Pokusy o připojení z Internetu a z Azure musí nejdříve projít přes bránu firewall předtím, než mají přístup k vaší databázi Azure Database for MariaDB, jak je znázorněno v následujícím diagramu:

![Příklad postupu fungování brány firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru se vztahují na všechny databáze v Azure Database pro MariaDB server.

Pokud IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení je povoleno.

Pokud IP adresa požadavku je mimo žádném rozsahů určených v pravidlech brány firewall na úrovni databáze nebo serveru, pak požadavek na připojení selže.

## <a name="connecting-from-azure"></a>Připojení z Azure
Chcete-li umožnit aplikacím z Azure připojení k Azure Database pro MariaDB server, musí být povolená připojení Azure. Například pro hostování aplikace Azure Web Apps nebo aplikace, která běží ve Virtuálním počítači Azure nebo k připojení ze Brána pro správu dat Azure Data Factory. Prostředky se nemusíte být ve stejné virtuální síti (VNet) nebo skupinu prostředků pro pravidlo brány firewall, aby tato připojení. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. Existuje několik metod, které chcete povolit tyto typy připojení. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete nastavit **povolit přístup ke službám Azure** umožňuje **ON** portálu ze **zabezpečení připojení** podokna a stiskněte klávesu **Uložit**. Pokud pokus o připojení není povolený, požadavek vůbec nedostane Azure Database pro MariaDB server.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

![Povolit přístup ke službám Azure konfigurovat na portálu](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě webu Azure portal můžete pravidla brány firewall prostřednictvím kódu programu spravovat pomocí rozhraní příkazového řádku Azure. <!--See also [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md)-->

## <a name="troubleshooting-the-database-firewall"></a>Řešení potíží s branou firewall databáze
Při přístupu k Microsoft Azure Database pro MariaDB server služby se nechová podle očekávání, zvažte následující body:

* **Změny do seznamu povolených ještě neprojevily:** může být tak, jak pět minut, než se změny do databáze Azure pro konfiguraci brány firewall serveru MariaDB se projeví.

* **Přihlášení není autorizováno nebo bylo nesprávné heslo:** Pokud přihlášení nemá oprávnění v Azure Database pro MariaDB server nebo použít heslo není správné, připojení k Azure Database pro MariaDB serveru byl odepřen. Vytvoření nastavení brány firewall klientům pouze poskytuje možnost pokusit se o připojení k vašemu serveru – každý klient musí dodat potřebné zabezpečené přihlašovací údaje.

* **Dynamická IP adresa:** Pokud máte připojení k Internetu dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jednu z následujících řešení:

* Požádejte poskytovatele služeb Internetu (ISP) o rozsah IP adres přidělený vašim klientským počítačům, které přistupují k Azure Database pro MariaDB server a pak přidat rozsah IP adres jako pravidlo brány firewall.

* Získejte pro své klientské počítače statické přidělování IP adres a následně přidejte tyto IP adresy jako pravidla brány firewall.

## <a name="next-steps"></a>Další postup
- [Vytváření a správa Azure Database pro MariaDB pravidla brány firewall pomocí webu Azure portal](./howto-manage-firewall-portal.md)

<!--
- [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md) -->
