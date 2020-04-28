---
title: Kurz – mapování stávající vlastní domény na jarní cloud Azure
description: Jak namapovat stávající vlastní název DNS (Distributed Name Service) na Azure jaře Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: 5b57a2463815d5db1c83d3bc4e8cd56314fb204d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176991"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapování stávající vlastní domény na jarní cloud Azure
Služba DNS (Distributed Name Service) je technika pro ukládání názvů síťových uzlů v síti. Tento kurz namapuje doménu, například www.contoso.com, pomocí záznamu CNAME. Zabezpečuje vlastní doménu s certifikátem a ukazuje, jak vymáhat protokol TLS (Transport Layer Security), označovaný také jako SSL (Secure Sockets Layer) (SSL). 

Certifikáty šifrují webový provoz. Tyto certifikáty TLS/SSL můžou být uložené v Azure Key Vault. 

## <a name="prerequisites"></a>Požadavky
* Aplikace nasazená do jarního cloudu Azure (Další informace najdete v tématu [rychlý Start: spuštění stávající aplikace pro jarní Cloud v Azure pomocí Azure Portal](spring-cloud-quickstart-launch-app-portal.md), nebo použití existující aplikace).
* Název domény s přístupem k registru DNS pro poskytovatele domény, jako je GoDaddy.
* Privátní certifikát od poskytovatele třetí strany. Certifikát se musí shodovat s doménou.
* Nasazená instance [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Import certifikátu 
Postup importu certifikátu vyžaduje, aby soubor s kódováním PEM nebo PFX byl na disku, a musíte mít privátní klíč. 

Postup nahrání certifikátu do trezoru klíčů:
1. Přejít do instance trezoru klíčů.
1. V levém navigačním podokně klikněte na **certifikáty**.
1. V horní nabídce klikněte na možnost **Generovat/importovat**.
1. V dialogovém okně **vytvořit certifikát** v části **Metoda vytvoření certifikátu**vyberte `Import`.
1. V části **nahrát soubor certifikátu**přejděte do umístění certifikátu a vyberte ho.
1. V části **heslo**zadejte privátní klíč certifikátu.
1. Klikněte na **Vytvořit**.

![Importovat certifikát 1](./media/custom-dns-tutorial/import-certificate-a.png)

Import certifikátu do jarního cloudu Azure:
1. Přejít na instanci služby. 
1. V levém navigačním podokně aplikace vyberte **Nastavení TLS/SSL**.
1. Pak klikněte na **importovat Key Vault certifikát**.

![Import certifikátu](./media/custom-dns-tutorial/import-certificate.png)

Po úspěšném Importování certifikátu se zobrazí seznam **certifikátů privátních klíčů**.

![Certifikát privátního klíče](./media/custom-dns-tutorial/key-certificates.png)

>[!IMPORTANT] 
> Chcete-li zabezpečit vlastní doménu pomocí tohoto certifikátu, je nutné vytvořit navázání certifikátu na konkrétní doménu. Postupujte podle kroků v tomto dokumentu pod nadpisem **Přidat vazbu SSL**.

## <a name="add-custom-domain"></a>Přidat vlastní doménu
Záznam CNAME můžete použít k mapování vlastního názvu DNS na jarní cloud Azure. 

> [!NOTE] 
> Záznam A se nepodporuje. 

### <a name="create-the-cname-record"></a>Vytvoření záznamu CNAME
Přejít na poskytovatele DNS a přidat záznam CNAME pro mapování domény na <service_name>. azuremicroservices.io. Tady <service_name> je název vaší instance Azure jaře cloudu. Podporujeme doménu se zástupnými znaky a podřízenou doménu. Po přidání CNAME bude stránka záznamů DNS vypadat podobně jako v následujícím příkladu: 

![Stránka záznamů DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapování vlastní domény na jarní cloudovou aplikaci Azure
Pokud nemáte aplikaci v Azure jaře cloudu, postupujte podle pokynů v části [rychlý Start: spuštění stávající aplikace Azure jaře cloudu pomocí Azure Portal](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Přejít na stránku aplikace

1. Vyberte **vlastní doména**.
2. Pak **přidejte vlastní doménu**. 

![Vlastní doména](./media/custom-dns-tutorial/custom-domain.png)

3. Zadejte plně kvalifikovaný název domény, pro který jste přidali záznam CNAME, například www.contoso.com. Ujistěte se, že typ záznamu názvu hostitele je nastavený na CNAME (<service_name>. azuremicroservices.io).
4. Kliknutím na tlačítko **ověřit** povolte tlačítko **Přidat** .
5. Klikněte na tlačítko **Add** (Přidat).

![Přidat vlastní doménu](./media/custom-dns-tutorial/add-custom-domain.png)

Jedna aplikace může mít víc domén, ale jedna doména se dá mapovat jenom na jednu aplikaci. Po úspěšném namapování vlastní domény k aplikaci se zobrazí v tabulce vlastní doména.

![Vlastní doménová tabulka](./media/custom-dns-tutorial/custom-domain-table.png)

>[!NOTE]
> **Nezabezpečený** popisek pro vaši vlastní doménu znamená, že ještě není vázaný na certifikát SSL. Jakékoli žádosti HTTPS z prohlížeče do vaší vlastní domény zobrazí chybu nebo varování.

## <a name="add-ssl-binding"></a>Přidat vazbu SSL
V tabulce vlastní doména vyberte **Přidat vazbu SSL** , jak je znázorněno na předchozím obrázku.  
1. Vyberte svůj **certifikát** nebo ho importujte.
1. Klikněte na **Uložit**.

![Přidat vazbu SSL](./media/custom-dns-tutorial/add-ssl-binding.png)

Po úspěšném přidání vazby SSL bude stav domény zabezpečený: **v pořádku**. 

![Přidat vazbu SSL](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Vynucení protokolu HTTPS
Ve výchozím nastavení může kdokoli k vaší aplikaci přistupovat pomocí protokolu HTTP, ale můžete přesměrovat všechny požadavky HTTP na port HTTPS.

Na stránce aplikace v levém navigačním panelu vyberte **vlastní doména**. Potom nastavte **pouze https**na *hodnotu true*.

![Přidat vazbu SSL](./media/custom-dns-tutorial/enforce-http.png)

Po dokončení operace přejděte na libovolnou adresu URL HTTPS, která odkazuje na vaši aplikaci. Upozorňujeme, že adresy URL HTTP nefungují.

## <a name="see-also"></a>Viz také
* [Co je Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Import certifikátu](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Spuštění vaší jarní cloudové aplikace pomocí Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

