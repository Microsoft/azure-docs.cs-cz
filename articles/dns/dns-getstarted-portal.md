---
title: 'Rychlý start: Vytvoření zóny a záznamu DNS pomocí webu Azure Portal'
description: Použijte tento rychlý start, abyste se naučili vytvořit zónu a záznam DNS v Azure DNS. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první zónu a záznam DNS pomocí webu Azure Portal.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: quickstart
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: 3ec2c44c168b47fd66d1ffa9a0c8d0069600ecb0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958097"
---
# <a name="quickstart-configure-azure-dns-for-name-resolution-using-the-azure-portal"></a>Rychlý start: Konfigurace Azure DNS k překladu názvů pomocí webu Azure Portal

 Azure DNS můžete nakonfigurovat k překladu názvů hostitelů ve veřejné doméně. Pokud jste například zakoupili název domény contoso.com od registrátora názvu domény, můžete nakonfigurovat Azure DNS k hostování domény contoso.com a přeložit www.contoso.com na IP adresu k webovému serveru nebo webové aplikaci.

V tomto rychlém startu vytvoříte testovací doménu a pak vytvoříte záznam adresy s názvem „www“, který ji přeloží na IP adresu 10.10.10.10.

Uvědomte si, že všechny názvy a IP adresy použité v tomto rychlém startu jsou jenom příklady a neodpovídají skutečným situacím. V určitých případech jsou ale popsané i skutečné situace.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

K uložení položek DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny položky (nebo záznamy) DNS pro vaši doménu se pak vytvoří v této zóně DNS. Následující kroky vám ukážou, jak na to.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

1. Přihlaste se k portálu Azure.
2. Vlevo nahoře klikněte na **+ Vytvořit prostředek**, dále na **Sítě** a pak klikněte na **Zóna DNS**. Otevře se stránka **Vytvořit zónu DNS**.

    ![Zóna DNS](./media/dns-getstarted-portal/openzone650.png)

4. Na stránce **Vytvořit zónu DNS** zadejte následující hodnoty a pak klikněte na **Vytvořit**:


   | **Nastavení** | **Hodnota** | **Podrobnosti** |
   |---|---|---|
   |**Název**|contoso.xyz|Název zóny DNS v tomto příkladu. V tomto rychlém startu můžete použít libovolnou hodnotu, která není nakonfigurovaná na serverech Azure DNS. Ve skutečné situaci byste použili doménu zakoupenou od registrátora názvu domény.|
   |**Předplatné**|[Vaše předplatné]|Vyberte předplatné, ve kterém chcete vytvořit zónu DNS.|
   |**Skupina prostředků**|**Vytvořit nový:** dns-test|Vytvořte skupinu prostředků. Název skupiny prostředků musí být v rámci vybraného předplatného jedinečný. |
   |**Umístění**|USA – východ||

Vytvoření zóny může trvat několik minut.

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

Nyní vytvořte nový záznam adresy (záznam „A“). Záznamy „A“ se používají k překladu názvu hostitele na adresu IPv4.

1. Na webu Azure Portal v podokně **Oblíbené** klikněte na **Všechny prostředky**. Na stránce Všechny prostředky klikněte na zónu DNS **contoso.xyz**. Pokud předplatné, které jste vybrali, již nějaké prostředky obsahovalo, můžete zadat **contoso.xyz** do pole **Filtrovat podle názvu...** pro snadný přístup k zóně DNS.

1. V horní části stránky **Zóna DNS** vyberte **Sada záznamů**. Otevře se stránka **Přidat sadu záznamů**.

1. Na stránce **Přidat sadu záznamů** zadejte následující hodnoty a klikněte na **OK**. V tomto příkladu vytvoříte záznam „A“.

   |**Nastavení** | **Hodnota** | **Podrobnosti** |
   |---|---|---|
   |**Název**|www|Název záznamu. Tento název použijete pro hostitele, kterého chcete přeložit na IP adresu.|
   |**Typ**|A| Typ záznamu DNS, který se má vytvořit. Záznamy „A“ jsou nejběžnější, ale existují i jiné typy záznamů pro poštovní servery (MX), adresy IPv6 (AAAA) atd. |
   |**Hodnota TTL**|1|Hodnota TTL (Time to Live) žádosti DNS. Určuje, po jak dlouhou dobu mohou DNS servery a klienti ukládat odpovědi do mezipaměti.|
   |**Jednotka hodnoty TTL**|hours|Měřené jednotky času pro hodnotu TTL.|
   |**IP adresa**|10.10.10.10| Tato hodnota je IP adresa, na kterou se záznam „A“ překládá. Jde pouze o testovací hodnotu pro účely tohoto rychlého startu. Ve skutečné situaci byste zadali veřejnou IP adresu vašeho webového serveru.|


Protože si v tomto rychlém startu skutečný název domény nekupujete, není nutné u svého registrátora názvu domény konfigurovat Azure DNS jako název serveru. Ve skutečné situaci by však měli být všichni uživatelé internetu schopní název hostitele přeložit, aby se mohli připojit k vašemu webovému serveru nebo aplikaci. Další informace o této skutečné situaci najdete v tématu [Delegování domény do Azure DNS](dns-delegate-domain-azure-dns.md).


## <a name="test-the-name-resolution"></a>Testování překladu IP adres

Když už máte testovací zónu se záznamem „A“, můžete překlad názvů otestovat nástrojem nslookup. 

1. Nejprve si poznamenejte názvové servery Azure DNS, které chcete v nástroji nslookup použít. 

   Názvové servery vaší zóny jsou uvedené na stránce **Přehled** zóny DNS. Zkopírujte název jednoho z názvových serverů:

   ![zóna](./media/dns-getstarted-portal/viewzonens500.png)

2. Otevřete příkazový řádek a spusťte následující příkaz:

   ```
   nslookup <host name> <name server>
   
   For example:

   nslookup www.contoso.xyz ns1-08.azure-dns.com
   ```

Mělo by se zobrazit něco podobného jako následující snímek obrazovky:

![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Tímto se ověřilo, že překlad názvů funguje správně. Adresa www.contoso.xyz se překládá na IP adresu 10.10.10.10 přesně tak, jak jste to nakonfigurovali.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, můžete všechny prostředky vytvořené v rámci tohoto rychlého startu odstranit odstraněním skupiny prostředků **dns-test**. Pokud chcete skupinu prostředků odstranit, klikněte na **dns-test** a pak klikněte na **Odstranit skupinu prostředků**.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)