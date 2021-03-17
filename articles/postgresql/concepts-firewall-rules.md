---
title: Pravidla brány firewall – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak použít pravidla brány firewall pro připojení k Azure Database for PostgreSQLmu jednomu serveru.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: ba353cf41cf3876a681f8f18d4121401260ff4ff
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98877166"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Pravidla brány firewall v Azure Database for PostgreSQL – jeden server
Azure Database for PostgreSQL Server je ve výchozím nastavení zabezpečený, aby se zabránilo všem přístupům k databázovému serveru, dokud neurčíte, ke kterým hostitelům IP mají mít přístup. Brána firewall uděluje přístup k serveru na základě zdrojové IP adresy jednotlivých požadavků.
Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

**Pravidla brány firewall:** Tato pravidla umožňují klientům přístup k celému serveru Azure Database for PostgreSQL, tedy ke všem databázím v rámci stejného logického serveru. Pravidla brány firewall na úrovni serveru se dají nakonfigurovat pomocí Azure Portal nebo pomocí příkazů rozhraní příkazového řádku Azure. Chcete-li vytvořit pravidla brány firewall na úrovni serveru, musíte být vlastníkem předplatného nebo přispěvatelem předplatného.

## <a name="firewall-overview"></a>Přehled brány firewall
Veškerý přístup k vašemu Azure Database for PostgreSQL serveru je ve výchozím nastavení blokovaný branou firewall. Chcete-li získat přístup k serveru z jiného počítače/klienta nebo aplikace, je nutné zadat jedno nebo více pravidel brány firewall na úrovni serveru pro povolení přístupu k serveru. Pomocí pravidel brány firewall určete povolené rozsahy veřejných IP adres. Přístup k samotnému webu Azure Portal nemá vliv na pravidla brány firewall.
Pokusy o připojení z Internetu a Azure musí nejdřív projít přes bránu firewall, aby se mohli připojit k databázi PostgreSQL, jak je znázorněno v následujícím diagramu:

:::image type="content" source="media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Příklad toku fungování brány firewall":::

## <a name="connecting-from-the-internet"></a>Připojení z Internetu
Pravidla brány firewall na úrovni serveru se vztahují na všechny databáze na stejném serveru Azure Database for PostgreSQL. Pokud je zdrojová IP adresa požadavku v jednom z rozsahů určených v pravidlech brány firewall na úrovni serveru, připojení je uděleno jinak, než je odmítnuto. Pokud se například vaše aplikace připojí pomocí ovladače JDBC pro PostgreSQL, může se při pokusu o připojení, když brána firewall blokuje připojení, setkat.
> java.util.concurrent.ExecutionException: Java. lang. RuntimeException –: org. PostgreSQL. util. PSQLException: ZÁVAŽNá: No pg \_ HBA. conf pro hostitele "123.45.67.890", "AdminUser", Database "PostgreSQL", SSL

## <a name="connecting-from-azure"></a>Připojení z Azure
Doporučujeme, abyste našli odchozí IP adresu libovolné aplikace nebo služby a výslovně povolili přístup k těmto IP adresám nebo rozsahům. Můžete například najít odchozí IP adresu Azure App Service nebo použít veřejnou IP adresu vázanou k virtuálnímu počítači nebo jinému prostředku (viz níže, kde najdete informace o připojení k privátní IP adrese virtuálního počítače prostřednictvím koncových bodů služby). 

Pokud pro vaši službu Azure není k dispozici pevná odchozí IP adresa, můžete zvážit povolení připojení ze všech IP adres datových center Azure. Toto nastavení se dá povolit z Azure Portal nastavením možnosti **Povolit přístup ke službám Azure** na **zapnuto** v podokně **zabezpečení připojení** a na **Uložit**. V rozhraní příkazového řádku Azure CLI platí, že nastavení pravidla brány firewall s počáteční a koncovou adresou rovnající se hodnotě 0.0.0.0. Pokud je pokus o připojení zamítnutý pravidly brány firewall, nedosáhne Azure Database for PostgreSQL serveru.

> [!IMPORTANT]
> Možnost **Povolení přístupu ke službám Azure** nakonfiguruje bránu firewall tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.
> 

:::image type="content" source="media/concepts-firewall-rules/allow-azure-services.png" alt-text="Konfigurace povolení přístupu ke službám Azure na portálu":::

## <a name="connecting-from-a-vnet"></a>Připojení z virtuální sítě
Pokud chcete bezpečně připojit k serveru Azure Database for PostgreSQL z virtuální sítě, zvažte použití [koncových bodů služby virtuální](./concepts-data-access-and-security-vnet.md)sítě. 

## <a name="programmatically-managing-firewall-rules"></a>Programová správa pravidel brány firewall
Kromě Azure Portal můžete pravidla brány firewall spravovat programově pomocí Azure CLI.
Viz také [Vytvoření a Správa pravidel brány firewall Azure Database for PostgreSQL pomocí Azure CLI](howto-manage-firewall-using-cli.md) .

## <a name="troubleshooting-firewall-issues"></a>Řešení potíží s bránou firewall
Vezměte v úvahu následující body, pokud se přístup k Microsoft Azure databázi pro službu serveru PostgreSQL nechová podle očekávání:

* **Změny v seznamu povolených se zatím neprojevily:** Změny konfigurace brány firewall serveru Azure Database for PostgreSQL se projeví až po dobu pěti minut.

* **Přihlášení není autorizováno nebo bylo použito nesprávné heslo:** Pokud přihlášení nemá oprávnění k serveru Azure Database for PostgreSQL nebo je použité heslo nesprávné, připojení k serveru Azure Database for PostgreSQL je odepřeno. Při vytváření nastavení brány firewall jsou jenom klienti s příležitostí k pokusu o připojení k vašemu serveru. Každý klient musí stále zadat potřebná zabezpečovací pověření.

   Například pomocí klienta JDBC se může zobrazit následující chyba.
   > java.util.concurrent.ExecutionException: Java. lang. RuntimeException –: org. PostgreSQL. util. PSQLException: ZÁVAŽNá: ověřování heslem pro uživatele "uživatelské_jméno" se nezdařilo.

* **Dynamická IP adresa:** Pokud vaše internetové připojení používá dynamické přidělování IP adres a máte problémy dostat se přes bránu firewall, můžete zkusit jedno z následujících řešení:

   * Zeptejte se poskytovatele internetových služeb (ISP) na rozsah IP adres přiřazený ke klientským počítačům, které přistupují k serveru Azure Database for PostgreSQL, a pak přidejte rozsah IP adres jako pravidlo brány firewall.

   * Místo toho Získejte statické IP adresy pro klientské počítače a pak přidejte statickou IP adresu jako pravidlo brány firewall.

* **IP adresa serveru je pravděpodobně veřejná:** Připojení k Azure Database for PostgreSQL serveru jsou směrována prostřednictvím veřejně přístupné brány Azure. Skutečná IP adresa serveru je však chráněná bránou firewall. Další informace najdete v [článku věnovaném architektuře připojení](concepts-connectivity-architecture.md).

* **Nejde se připojit z prostředku Azure s povolenou IP adresou:** Ověřte, jestli je povolený koncový bod služby **Microsoft. SQL** pro podsíť, ze které se připojujete. Pokud je **Microsoft. SQL** povolený, znamená to, že chcete v této podsíti použít jenom [pravidla koncových bodů služby virtuální](concepts-data-access-and-security-vnet.md) sítě.

   Například se může zobrazit následující chyba, pokud se připojujete z virtuálního počítače Azure v podsíti s povoleným **Microsoft. SQL** , ale nemá žádné odpovídající pravidlo virtuální sítě:  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* **Pro formát IPv6 není k dispozici pravidlo brány firewall:** Pravidla brány firewall musí být ve formátu IPv4. Pokud zadáte pravidla brány firewall ve formátu protokolu IPv6, zobrazí se chyba ověřování.


## <a name="next-steps"></a>Další kroky
* [Vytváření a Správa Azure Database for PostgreSQL pravidel brány firewall pomocí Azure Portal](howto-manage-firewall-using-portal.md)
* [Vytvoření a Správa pravidel brány firewall Azure Database for PostgreSQL pomocí rozhraní příkazového řádku Azure](howto-manage-firewall-using-cli.md)
* [Koncové body služby virtuální sítě v Azure Database for PostgreSQL](./concepts-data-access-and-security-vnet.md)
