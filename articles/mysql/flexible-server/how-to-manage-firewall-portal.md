---
title: Správa pravidel brány firewall-Azure Portal-Azure Database for MySQL-flexibilní Server
description: Vytvoření a Správa pravidel brány firewall pro Azure Database for MySQL flexibilní Server pomocí Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b26ce91b005fc7bd4d5b89ccf5306dc03a040b0f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106746"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Vytvoření a Správa pravidel brány firewall pro Azure Database for MySQL flexibilní Server pomocí Azure Portal

> [!IMPORTANT]
> Azure Database for MySQL flexibilní Server je momentálně ve verzi Public Preview.

Flexibilní server Azure Database for MySQL podporuje dva typy vzájemně se vylučujících metod síťového připojení k flexibilnímu serveru. Tyto dvě možnosti jsou následující:

1. Veřejný přístup (povolené IP adresy)
2. Privátní přístup (integrace virtuální sítě)

V tomto článku se zaměříme na vytvoření serveru MySQL s **veřejným přístupem (s povolenými IP adresami)** pomocí Azure Portal a poskytneme vám přehled o správě pravidel brány firewall po vytvoření flexibilního serveru. Díky *veřejnému přístupu (povoleným IP adresám)* se připojení k serveru MySQL omezují jenom na povolená IP adresa. IP adresy klienta musí být povoleny v pravidlech brány firewall. Další informace o této službě najdete v tématu [veřejný přístup (povolené IP adresy)](./concepts-networking.md#public-access-allowed-ip-addresses). Pravidla brány firewall lze definovat v době vytváření serveru (doporučeno), ale lze je také přidat později. V tomto článku budeme poskytovat přehled o tom, jak vytvářet a spravovat pravidla brány firewall pomocí veřejného přístupu (povolené IP adresy).

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Vytvoření pravidla brány firewall při vytváření serveru

1. V levém horním rohu portálu vyberte **vytvořit prostředek** (+).
2. Vyberte **databáze**  >  **Azure Database for MySQL**. Službu můžete vyhledat také zadáním **MySQL** do vyhledávacího pole.
3. Jako možnost nasazení vyberte **flexibilní Server** .
4. Vyplňte formulář **základy** .
5. Chcete-li nakonfigurovat, jak se chcete připojit k serveru, přejdete na kartu **sítě** .
6. V části **metoda připojení** vyberte *veřejný přístup (povolené IP adresy)*. Chcete-li vytvořit **pravidla brány firewall**, zadejte název pravidla brány firewall a jednu IP adresu nebo rozsah adres. Pokud chcete pravidlo omezit na jednu IP adresu, zadejte v poli Počáteční IP adresa a koncovou IP adresu stejnou adresu. Otevření brány firewall umožňuje správcům, uživatelům a aplikacím přístup k jakékoli databázi na serveru MySQL, ke které mají platné přihlašovací údaje.
   > [!Note]
   > Azure Database for MySQL flexibilní Server vytvoří bránu firewall na úrovni serveru. Ta brání externím aplikacím a nástrojům v připojení k serveru a kterékoli databázi na serveru, pokud nevytvoříte pravidlo k otevření brány firewall pro konkrétní IP adresy.

7. Vyberte možnost **zkontrolovat + vytvořit** a zkontrolujte flexibilní konfiguraci serveru.
8.  Vyberte **Vytvořit**, aby se server zřídil. Zřizování může trvat několik minut.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Po vytvoření serveru vytvořit pravidlo brány firewall

1. V [Azure Portal](https://portal.azure.com/)vyberte Azure Database for MySQL flexibilní Server, na který chcete přidat pravidla brány firewall.
2. Na stránce flexibilní Server klikněte v části **Nastavení** na položku **sítě** a otevřete stránku síť pro flexibilní Server.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/1-connection-security.png" alt-text="Azure portal - click Connection Security":::-->

3. V pravidlech brány firewall klikněte na **Přidat aktuální IP adresu klienta** . Tím se automaticky vytvoří pravidlo brány firewall s veřejnou IP adresou vašeho počítače, jak je znázorněno v systému Azure.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/2-add-my-ip.png" alt-text="Azure portal - click Add My IP":::-->

4. Před uložením konfigurace ověřte svoji IP adresu. V některých situacích se IP adresa zjištěná Azure Portal liší od IP adresy používané při přístupu k Internetu a k serverům Azure. Proto může být nutné změnit počáteční IP adresu a koncovou IP adresu, aby pravidlo fungovalo podle očekávání.

   K kontrole vlastní IP adresy můžete použít vyhledávací modul nebo jiný online nástroj. Vyhledejte například "Co je moje IP adresa".

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/3-what-is-my-ip.png" alt-text="Bing search for What is my IP":::-->

5. Přidejte další rozsahy adres. V pravidlech brány firewall pro Azure Database for MySQL flexibilní Server můžete zadat jednu IP adresu nebo rozsah adres. Pokud chcete pravidlo omezit na jednu IP adresu, zadejte v poli Počáteční IP adresa a koncovou IP adresu stejnou adresu. Otevření brány firewall umožňuje správcům, uživatelům a aplikacím přístup k jakékoli databázi na serveru MySQL, ke které mají platné přihlašovací údaje.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/4-specify-addresses.png" alt-text="Azure portal - firewall rules":::-->

6. Kliknutím na **Uložit** na panelu nástrojů uložte toto pravidlo brány firewall. Počkejte, až se potvrdí, že aktualizace pravidel firewallu proběhla úspěšně.

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/5-save-firewall-rule.png" alt-text="Azure portal - click Save":::-->

## <a name="connect-from-azure"></a>Připojení z Azure

Možná budete chtít povolit prostředky nebo aplikace nasazené v Azure, abyste se mohli připojit k flexibilnímu serveru. To zahrnuje webové aplikace hostované v Azure App Service, spuštěné na virtuálním počítači Azure, bránu pro správu dat Azure Data Factory a spoustu dalších.

Když se aplikace v Azure pokusí připojit k serveru, brána firewall ověří, jestli jsou povolená připojení Azure. Toto nastavení můžete povolit tak, že vyberete možnost **Povolit veřejný přístup ze služeb Azure a prostředků v rámci Azure do tohoto serveru** na portálu na kartě **sítě** a potom stiskněte **Uložit**.

Aby bylo možné tato připojení povolit, nemusí být prostředky ve stejné virtuální síti (VNet) nebo skupině prostředků pro pravidlo brány firewall. Pokud se pokus o připojení nepovoluje, požadavek nedosáhne Azure Database for MySQL flexibilnímu serveru.

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup jenom na autorizované uživatele.
>
> Pro zabezpečený přístup k flexibilnímu serveru doporučujeme zvolit **privátní přístup (Integration VNET)** .
>

## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Spravujte existující pravidla brány firewall pomocí Azure Portal

Opakováním následujících kroků spravujte pravidla brány firewall.

- Chcete-li přidat aktuální počítač, klikněte na + **Přidat aktuální IP adresu klienta** v pravidlech brány firewall. Kliknutím na **Uložit** uložte změny.
- Pro přidání dalších IP adres zadejte Název pravidla, Počáteční IP adresu a Koncovou IP adresu. Kliknutím na **Uložit** uložte změny.
- Pokud chcete upravit stávající pravidlo, klikněte na libovolné pole pravidla a upravte ho. Kliknutím na **Uložit** uložte změny.
- Chcete-li odstranit stávající pravidlo, klikněte na tlačítko se třemi tečkami [...] a odstraňte pravidlo kliknutím na tlačítko **Odstranit** . Kliknutím na **Uložit** uložte změny.

## <a name="next-steps"></a>Další kroky
- Další informace o [sítích v Azure Database for MySQL flexibilním serveru](./concepts-networking.md)
- Další informace o [Azure Database for MySQL flexibilních pravidlech brány firewall serveru](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Vytvářejte a spravujte Azure Database for MySQL pravidla brány firewall pomocí Azure CLI](./how-to-manage-firewall-cli.md).