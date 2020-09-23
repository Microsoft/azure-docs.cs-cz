---
title: Pravidla brány firewall – Azure Database for MySQL
description: Přečtěte si, jak pomocí pravidel brány firewall povolit připojení k vašemu Azure Database for MySQL serveru.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 63b3fd64526f45994919267a2f4ddc730d0b2fd7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882666"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Pravidla brány firewall serveru Azure Database for MySQL
Brány firewall zabraňují všem přístupům k databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru na základě zdrojové IP adresy jednotlivých požadavků.

Chcete-li nakonfigurovat bránu firewall, vytvořte pravidla brány firewall, která určují rozsah přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k celému serveru Azure Database for MySQL, tedy ke všem databázím v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru se dají nakonfigurovat pomocí příkazů Azure Portal nebo rozhraní příkazového řádku Azure. Chcete-li vytvořit pravidla brány firewall na úrovni serveru, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Přístup všech databází k serveru Azure Database for MySQL je ve výchozím nastavení blokován bránou firewall. Pokud chcete začít používat server z jiného počítače, musíte zadat jedno nebo více pravidel brány firewall na úrovni serveru, abyste mohli povolit přístup k vašemu serveru. Pomocí pravidel brány firewall určete, které rozsahy IP adres z Internetu mají být povoleny. Přístup k samotnému webu Azure Portal nemá vliv na pravidla brány firewall.

Pokusy o připojení z Internetu a Azure musí nejdřív projít přes bránu firewall, aby mohli získat přístup k vaší Azure Database for MySQL databázi, jak je znázorněno v následujícím diagramu:

:::image type="content" source="./media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Příklad toku fungování brány firewall":::

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru se vztahují na všechny databáze na serveru Azure Database for MySQL.

Pokud je IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení se udělí.

Pokud je IP adresa požadavku mimo rozsahy zadané v pravidlech brány firewall na úrovni databáze nebo serveru, požadavek na připojení se nezdařil.

## <a name="connecting-from-azure"></a>Připojení z Azure
Doporučujeme, abyste našli odchozí IP adresu libovolné aplikace nebo služby a výslovně povolili přístup k těmto IP adresám nebo rozsahům. Můžete například najít odchozí IP adresu Azure App Service nebo použít veřejnou IP adresu vázanou k virtuálnímu počítači nebo jinému prostředku (viz níže, kde najdete informace o připojení k privátní IP adrese virtuálního počítače prostřednictvím koncových bodů služby). 

Pokud pro vaši službu Azure není k dispozici pevná odchozí IP adresa, můžete zvážit povolení připojení ze všech IP adres datových center Azure. Toto nastavení se dá povolit z Azure Portal nastavením možnosti **Povolit přístup ke službám Azure** na **zapnuto** v podokně **zabezpečení připojení** a na **Uložit**. V rozhraní příkazového řádku Azure CLI platí, že nastavení pravidla brány firewall s počáteční a koncovou adresou rovnající se hodnotě 0.0.0.0. Pokud se pokus o připojení nepovoluje, požadavek nedosáhne serveru Azure Database for MySQL.

> [!IMPORTANT]
> Možnost **Povolení přístupu ke službám Azure** nakonfiguruje bránu firewall tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.
> 

:::image type="content" source="./media/concepts-firewall-rules/allow-azure-services.png" alt-text="Konfigurace povolení přístupu ke službám Azure na portálu":::

### <a name="connecting-from-a-vnet"></a>Připojení z virtuální sítě
Pokud chcete bezpečně připojit k serveru Azure Database for MySQL z virtuální sítě, zvažte použití [koncových bodů služby virtuální](./concepts-data-access-and-security-vnet.md)sítě. 

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě Azure Portal můžete pravidla brány firewall spravovat programově pomocí rozhraní příkazového řádku Azure CLI. Viz také [Vytvoření a Správa pravidel brány firewall Azure Database for MySQL pomocí Azure CLI](./howto-manage-firewall-using-cli.md) .

## <a name="troubleshooting-firewall-issues"></a>Řešení potíží s bránou firewall
Pokud se přístup ke službě Microsoft Azure Database for MySQL server nechová podle očekávání, vezměte v úvahu následující body:

* **Změny v seznamu povolených se zatím neprojevily:** Změny konfigurace brány firewall serveru Azure Database for MySQL se projeví až po dobu pěti minut.

* **Přihlášení není autorizováno nebo bylo použito nesprávné heslo:** Pokud přihlášení nemá oprávnění k serveru Azure Database for MySQL nebo je použité heslo nesprávné, připojení k serveru Azure Database for MySQL je odepřeno. Vytvoření nastavení brány firewall klientům pouze poskytuje možnost pokusit se o připojení k vašemu serveru – každý klient musí dodat potřebné zabezpečené přihlašovací údaje.

* **Dynamická IP adresa:** Pokud máte připojení k Internetu s dynamickým IP adresou a máte potíže s připojením přes bránu firewall, můžete vyzkoušet jedno z následujících řešení:

   * Zeptejte se poskytovatele internetových služeb (ISP) na rozsah IP adres přiřazený ke klientským počítačům, které přistupují k serveru Azure Database for MySQL, a pak přidejte rozsah IP adres jako pravidlo brány firewall.

   * Získejte pro své klientské počítače statické přidělování IP adres a následně přidejte tyto IP adresy jako pravidla brány firewall.

* **IP adresa serveru je pravděpodobně veřejná:** Připojení k Azure Database for MySQL serveru jsou směrována prostřednictvím veřejně přístupné brány Azure. Skutečná IP adresa serveru je však chráněná bránou firewall. Další informace najdete v [článku věnovaném architektuře připojení](concepts-connectivity-architecture.md). 

* **Nejde se připojit z prostředku Azure s povolenou IP adresou:** Ověřte, jestli je povolený koncový bod služby **Microsoft. SQL** pro podsíť, ze které se připojujete. Pokud je **Microsoft. SQL** povolený, znamená to, že chcete v této podsíti použít jenom [pravidla koncových bodů služby virtuální](concepts-data-access-and-security-vnet.md) sítě.

   Například se může zobrazit následující chyba, pokud se připojujete z virtuálního počítače Azure v podsíti s povoleným **Microsoft. SQL** , ale nemá žádné odpovídající pravidlo virtuální sítě:  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

## <a name="next-steps"></a>Další kroky

* [Vytváření a Správa Azure Database for MySQL pravidel brány firewall pomocí Azure Portal](./howto-manage-firewall-using-portal.md)
* [Vytvoření a Správa pravidel brány firewall Azure Database for MySQL pomocí rozhraní příkazového řádku Azure](./howto-manage-firewall-using-cli.md)
* [Koncové body služby virtuální sítě v Azure Database for MySQL](./concepts-data-access-and-security-vnet.md)
