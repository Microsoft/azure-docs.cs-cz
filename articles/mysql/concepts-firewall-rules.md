---
title: Pravidla brány firewall serveru Azure Database for MySQL
description: Popisuje pravidla brány firewall pro server Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0802185b7fb0d1a6d7d41cd1fa5a30f5ce10424b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443916"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Pravidla brány firewall serveru Azure Database for MySQL
Brány firewall zabraňují všem přístupům k databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru na základě zdrojové IP adresy jednotlivých požadavků.

Chcete-li nakonfigurovat bránu firewall, vytvořte pravidla brány firewall, která určují rozsah přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k celému serveru Azure Database for MySQL, tedy ke všem databázím v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru se dají nakonfigurovat pomocí příkazů Azure Portal nebo rozhraní příkazového řádku Azure. Chcete-li vytvořit pravidla brány firewall na úrovni serveru, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Přístup všech databází k serveru Azure Database for MySQL je ve výchozím nastavení blokován bránou firewall. Pokud chcete začít používat server z jiného počítače, musíte zadat jedno nebo více pravidel brány firewall na úrovni serveru, abyste mohli povolit přístup k vašemu serveru. Pomocí pravidel brány firewall určete, které rozsahy IP adres z Internetu mají být povoleny. Přístup k samotnému webu Azure Portal nemá vliv na pravidla brány firewall.

Pokusy o připojení z Internetu a Azure musí nejdřív projít přes bránu firewall, aby mohli získat přístup k vaší Azure Database for MySQL databázi, jak je znázorněno v následujícím diagramu:

![Příklad toku fungování brány firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru se vztahují na všechny databáze na serveru Azure Database for MySQL.

Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení se udělí.

Pokud je IP adresa požadavku mimo rozsahy zadané v pravidlech brány firewall na úrovni databáze nebo serveru, požadavek na připojení se nezdařil.

## <a name="connecting-from-azure"></a>Připojení z Azure
Pokud chcete aplikacím z Azure povolit připojení k vašemu Azure Database for MySQL serveru, musí být povolená připojení Azure. Například pro hostování aplikace Web Apps v Azure nebo aplikace, která běží na virtuálním počítači Azure, nebo pro připojení z Azure Data Factory brány pro správu dat. Aby se tato připojení dala povolit, nemusíte být prostředky ve stejné Virtual Network (virtuální síti) nebo skupině prostředků pro pravidlo brány firewall. Když se aplikace z Azure pokusí připojit k vašemu databázovému serveru, brána firewall ověří, zda jsou povolená připojení Azure. K dispozici je několik metod, jak tyto typy připojení povolit. Nastavení brány firewall s počáteční i koncovou adresou odpovídající 0.0.0.0 znamená, že jsou tato připojení povolená. Alternativně můžete na portálu v podokně **zabezpečení připojení** nastavit možnost **Povolení přístupu ke službám Azure** na **zapnuto** a potom kliknout na **Uložit**. Pokud se pokus o připojení nepovoluje, požadavek nedosáhne serveru Azure Database for MySQL.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

![Konfigurace povolení přístupu ke službám Azure na portálu](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě Azure Portal můžete pravidla brány firewall spravovat programově pomocí rozhraní příkazového řádku Azure CLI. Viz také [Vytvoření a Správa pravidel brány firewall Azure Database for MySQL pomocí Azure CLI](./howto-manage-firewall-using-cli.md) .

## <a name="troubleshooting-the-database-firewall"></a>Řešení potíží s branou firewall databáze
Pokud se přístup ke službě Microsoft Azure Database for MySQL server nechová podle očekávání, vezměte v úvahu následující body:

* **Změny v seznamu povolených se zatím neprojevily:** Změny konfigurace brány firewall serveru Azure Database for MySQL se projeví až po dobu pěti minut.

* **Přihlášení není autorizováno nebo bylo použito nesprávné heslo:** Pokud přihlášení nemá oprávnění k serveru Azure Database for MySQL nebo je použité heslo nesprávné, připojení k serveru Azure Database for MySQL je odepřeno. Vytvoření nastavení brány firewall klientům pouze poskytuje možnost pokusit se o připojení k vašemu serveru – každý klient musí dodat potřebné zabezpečené přihlašovací údaje.

* **Dynamická IP adresa:** Pokud máte připojení k Internetu s dynamickým IP adresou a máte potíže s připojením přes bránu firewall, můžete vyzkoušet jedno z následujících řešení:

* Zeptejte se poskytovatele internetových služeb (ISP) na rozsah IP adres přiřazený ke klientským počítačům, které přistupují k serveru Azure Database for MySQL, a pak přidejte rozsah IP adres jako pravidlo brány firewall.

* Získejte pro své klientské počítače statické přidělování IP adres a následně přidejte tyto IP adresy jako pravidla brány firewall.

## <a name="next-steps"></a>Další postup

* [Vytváření a Správa Azure Database for MySQL pravidel brány firewall pomocí Azure Portal](./howto-manage-firewall-using-portal.md)
* [Vytvoření a Správa pravidel brány firewall Azure Database for MySQL pomocí rozhraní příkazového řádku Azure](./howto-manage-firewall-using-cli.md)
