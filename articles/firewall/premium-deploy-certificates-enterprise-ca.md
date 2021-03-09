---
title: Nasazení a konfigurace certifikátů certifikační autority organizace pro Azure Firewall Premium Preview
description: Přečtěte si, jak nasadit a nakonfigurovat certifikáty podnikové CA pro Azure Firewall Premium Preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525474"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Nasazení a konfigurace certifikátů certifikační autority organizace pro Azure Firewall Preview

> [!IMPORTANT]
> Azure Firewall Premium je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Azure Firewall Premium Preview zahrnuje funkci kontroly TLS, která vyžaduje řetěz certifikátů ověřování. V produkčních nasazeních byste měli použít infrastrukturu veřejných klíčů rozlehlé sítě k vygenerování certifikátů, které používáte s Azure Firewall Premium. Pomocí tohoto článku můžete vytvořit a spravovat certifikát zprostředkující certifikační autority pro Azure Firewall Premium Preview.

Další informace o certifikátech, které používá Azure Firewall Premium Preview, najdete v článku [certifikáty Azure firewall Premium Preview](premium-certificates.md).

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud chcete použít certifikační autoritu organizace k vygenerování certifikátu pro použití s Azure Firewall Premium Preview, musíte mít následující prostředky: 

- Doménová struktura služby Active Directory 
- Kořenová certifikační autorita služby Active Directory s povoleným webovým zápisem 
- Azure Firewall Premium se zásadami brány firewall úrovně Premium 
- Azure Key Vault 
- Spravovaná identita s oprávněním pro čtení k **certifikátům a tajným klíčům** definovaným v zásadách přístupu Key Vault 

## <a name="request-and-export-a-certificate"></a>Vyžádání a Export certifikátu

1. Přihlaste se k webu webového zápisu na kořenové certifikační autoritě, obvykle `https://<servername>/certsrv` a vyberte **požadovat certifikát**.
1. Vyberte **Upřesnit žádost o certifikát**.
1. Vyberte **vytvořit a odeslat žádost této certifikační autoritě**.
1. Vyplňte formulář pomocí šablony podřízené certifikační autority, jak je znázorněno na následujícím obrázku:
1. Odešlete žádost a nainstalujte certifikát.
1. Za předpokladu, že se tento požadavek provede z Windows serveru pomocí Internet Exploreru, otevřete **Možnosti Internetu**.
1. Přejděte na kartu **obsah** a vyberte možnost **certifikáty**.
1. Vyberte certifikát, který jste právě vystavili, a pak vyberte **exportovat**.
1. Kliknutím na tlačítko **Další** zahájíte průvodce. Vyberte **Ano, exportovat privátní klíč** a pak vyberte **Další**.
1. ve výchozím nastavení je vybraný formát souboru. pfx. Zrušte kontrolu **včetně všech certifikátů v cestě k certifikátu, pokud je to možné**. Pokud exportujete celý řetěz certifikátů, proces importu do Azure Firewall se nezdaří.
1. Přiřaďte a potvrďte heslo pro ochranu klíče a pak vyberte **Další**.
1. Zvolte název souboru a umístění pro export a pak vyberte **Další**.
1. Vyberte **Dokončit** a přesuňte exportovaný certifikát do zabezpečeného umístění.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Přidání certifikátu do zásad brány firewall

1. V Azure Portal přejděte na stránku certifikáty v Key Vault a vyberte **Generovat/importovat**.
1. Vyberte **importovat** jako metodu vytvoření, název certifikátu, vyberte exportovaný soubor. pfx, zadejte heslo a pak vyberte **vytvořit**.
1. Přejděte na stránku **Kontrola TLS (Preview)** v zásadách brány firewall a vyberte spravovanou identitu, Key Vault a certifikát. 
1. Vyberte **Uložit**.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="Kontrola TLS":::

## <a name="validate-tls-inspection"></a>Ověřit kontrolu TLS

1. Vytvořte pravidlo aplikace pomocí kontroly TLS na cílovou adresu URL nebo plně kvalifikovaný název domény podle vašeho výběru.  Příklad: `*bing.com`.
1. Z počítače připojeného k doméně v rámci zdrojového rozsahu pravidla přejděte do svého cíle a vyberte symbol zámku vedle panelu Adresa v prohlížeči. Certifikát by měl ukázat, že vystavila vaše podniková certifikační autorita, ale ne veřejnou CA.
1. Zobrazit certifikát pro zobrazení dalších podrobností, včetně cesty k certifikátu.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="Podrobnosti o certifikátu":::
1. V Log Analytics spusťte následující dotaz KQL, který vrátí všechny požadavky, které byly podrobeny kontrole TLS:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   Výsledek zobrazuje úplnou adresu URL kontrolovaného provozu: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="dotaz KQL":::

## <a name="next-steps"></a>Další kroky

[Azure Firewall Premium Preview v Azure Portal](premium-portal.md)
