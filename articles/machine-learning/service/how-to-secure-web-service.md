---
title: Zabezpečení webových služeb pomocí SSL
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak zabezpečit webové služby nasazené ve službě Azure Machine Learning povolením protokolu HTTPS. HTTPS zabezpečuje data odeslaná klienty, kteří používají zabezpečení transportní vrstvy (TLS), můžou nahradit aktuální soubor vrstvy zabezpečení soketu (SSL). Je také používají klienti k ověření identity webové služby.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1a6aa75f3d25cd88cd1edb9b2cdcfabc3b4ec8f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818547"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Použití protokolu SSL pro zabezpečení webové služby pomocí služby Azure Machine Learning

V tomto článku se dozvíte, jak zabezpečit webovou službu nasazenou ve službě Azure Machine Learning. Můžete omezit přístup k webovým službám a zabezpečit data odeslaná klienty, kteří používají [protokol zabezpečení HTTPS (Hypertext Transfer)](https://en.wikipedia.org/wiki/HTTPS).

Protokol HTTPS se používá k zabezpečení komunikace mezi klientem a webovou službu pomocí šifrování komunikace mezi nimi. Šifrování se určují pomocí šablon [zabezpečení TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Někdy stále označuje se jako vrstva SSL (Secure Sockets), který byl předchůdce protokol TLS.

> [!TIP]
> Sada SDK Azure Machine Learning používá termín "protokol SSL' pro vlastnosti týkající se povolení zabezpečené komunikace. To neznamená, že webová služba nepoužívá protokol TLS, stačí tento protokol SSL je více rozpoznatelných termín pro mnoho čtenáři.

TLS a SSL oba spoléhají na __digitální certifikáty__, které jsou používány k provádění ověření šifrování a identity. Další informace o práci způsob, jakým digitální certifikáty, naleznete v příspěvku Wikipedia na [infrastruktury veřejných klíčů (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Pokud nemáte povolení a používání protokolu HTTPS pro webovou službu, může být k ostatním na Internetu vidět data odesílaná do a ze služby.
>
> HTTPS také umožňuje klientovi k ověření pravosti, která se připojuje k serveru. Tím se zajistí ochrana klientů proti [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) útoky.

Postup zabezpečení nové webové služby nebo některý z existujících je následující:

1. Získáte název domény.

2. Získáte digitální certifikát.

3. Nasazení nebo aktualizovat webovou službu s povoleným nastavením SSL.

4. Aktualizujte svoji službu DNS tak, aby odkazoval na webovou službu.

Existují mírné rozdíly při zabezpečování webových služeb napříč [cíle nasazení](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Získání názvu domény

Pokud jste již nevlastní název domény, můžete si zakoupit jeden z __registrátora názvu domény__. Proces se liší mezi registrátorů, stejně jako náklady. Doménový Registrátor také poskytuje nástroje pro správu název domény. Tyto nástroje se používají k mapování plně kvalifikovaný název domény (jako je například www\.contoso.com) na IP adresu, který je hostitelem webové služby.

## <a name="get-an-ssl-certificate"></a>Získat certifikát SSL

Existuje mnoho způsobů, jak získat certifikát SSL (certifikát). Nejběžnější je to k nákupu z __certifikační autority__ (CA). Bez ohledu na to, kde můžete získat certifikát budete potřebovat následující soubory:

* A __certifikát__. Certifikát musí obsahovat řetězce úplný certifikát a musí být kódovaný PEM.
* A __klíč__. Klíč musí být kódovaný PEM.

Při žádosti o certifikát, musíte plně kvalifikovaný název domény (FQDN) adresy, které chcete použít pro webovou službu. Například www\.contoso.com. Porovnání adres razítkem do certifikát a adresu použitou klienty při ověřování identity webové služby. Pokud adresy se neshodují, klienti obdrží chybu.

> [!TIP]
> Pokud certifikační autorita nemůže poskytnout certifikát a klíč jako kódovaný PEM soubory, můžete použít nástroj, jako [OpenSSL](https://www.openssl.org/) změny formátu.

> [!WARNING]
> Certifikáty podepsané svým držitelem by měla sloužit pouze pro vývoj. Není vhodné používat v produkčním prostředí. Certifikáty podepsané svým držitelem může způsobovat problémy v klientovi aplikace. Další informace naleznete v dokumentaci pro knihovny sítě použité v klientské aplikaci.

## <a name="enable-ssl-and-deploy"></a>Povolení protokolu SSL a nasazení

Chcete-li nasadit (nebo znovu nasadit) na službu s protokol SSL povolený, nastavte `ssl_enabled` parametr `True`, bez ohledu na to použít. Nastavte `ssl_certificate` parametr na hodnotu __certifikát__ souboru a `ssl_key` na hodnotu __klíč__ souboru.

+ **Nasazení ve službě Azure Kubernetes Service (AKS)**

  Při zřizování clusteru AKS, zadejte hodnoty pro parametry související s protokolem SSL, jak znázorňuje fragment kódu:

    ```python
    from azureml.core.compute import AksCompute

    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Nasazení do Azure Container Instances (ACI)**

  Při nasazování do služby ACI, zadejte hodnoty pro parametry související s protokolem SSL, jak znázorňuje fragment kódu:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Nasazení na pole Programmable Gate Array (FPGA)**

  Při nasazování do FPGA, zadejte hodnoty pro parametry související s protokolem SSL, jak znázorňuje fragment kódu:

    ```python
    from azureml.contrib.brainwave import BrainwaveWebservice

    deployment_config = BrainwaveWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem")
    ```

## <a name="update-your-dns"></a>Aktualizujte svoji službu DNS

V dalším kroku je nutné aktualizovat DNS tak, aby odkazoval na webovou službu.

+ **Pro ACI**:

  Použijte nástroje poskytované systémem registrátora názvu domény aktualizovat záznam DNS pro název domény. Záznam musí odkazovat na IP adresu služby.

  V závislosti na doménový Registrátor a čas live (TTL) nakonfigurovaný pro název domény, může trvat několik minut až několik hodin, než klienti mohli přeložit název domény.

+ **Pro AKS**:

  Aktualizace DNS na kartě "Konfigurace" z "Veřejné IP adresy" clusteru AKS, jak je znázorněno na obrázku. Veřejnou IP adresu najdete jako jeden z typů prostředků vytvořené v rámci skupiny prostředků, která obsahuje agentské uzly AKS a jiných síťových prostředků.

  ![Služba Azure Machine Learning: Zabezpečení webové služby s protokolem SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Další postup
Naučte se:
+ [Využívání služby machine learning model nasadit jako webovou službu](how-to-consume-web-service.md)
+ [Bezpečné spuštění experimentů a odvozování uvnitř virtuální sítě Azure](how-to-enable-virtual-network.md)
