---
title: Nastavení instance konfiguračního serveru v Azure jaře cloudu
description: Naučte se nastavit instanci jarního cloudového konfiguračního serveru pro váš jarní cloud Azure na Azure Portal
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: 2f788452455bfbbc47f0a48689ccf3344515fdae
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904249"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Nastavení instance jarního cloudového konfiguračního serveru pro vaši službu

**Tento článek se týká:** ✔️ Java ✔️ C #

V tomto článku se dozvíte, jak připojit instanci jarního cloudového konfiguračního serveru k vaší jarní cloudové službě Azure.

Jarní cloudová konfigurace poskytuje podporu na straně serveru a klienta pro externou konfiguraci v distribuovaném systému. V případě instance konfiguračního serveru máte centrální místo pro správu externích vlastností pro aplikace ve všech prostředích. Další informace najdete v tématu [Reference k serveru pro konfiguraci jarního cloudu](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Již zřízené a běžící cloudová služba Azure. Pokud chcete nastavit a spustit službu jarních cloudů Azure, přečtěte si [rychlý Start: spuštění aplikace pružiny v jazyce Java pomocí rozhraní příkazového řádku Azure](spring-cloud-quickstart.md).

## <a name="restriction"></a>Omezení

K dispozici jsou určitá omezení, pokud používáte konfigurační server s back-end Git. Některé vlastnosti jsou automaticky vloženy do prostředí aplikace pro přístup ke konfiguračnímu serveru a zjišťování služby. Pokud tyto vlastnosti nakonfigurujete i ze souborů konfiguračního serveru, může docházet ke konfliktům a neočekávanému chování. Mezi vlastnosti patří: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> Důrazně doporučujeme, abyste výše uvedené vlastnosti _neumístili_ do souborů aplikace konfiguračního serveru.

## <a name="create-your-config-server-files"></a>Vytvoření souborů konfiguračního serveru

Azure jaře Cloud podporuje Azure DevOps, GitHub, GitLab a Bitbucket pro ukládání souborů konfiguračního serveru. Po přípravě úložiště vytvořte konfigurační soubory podle následujících pokynů a uložte je tam.

Kromě toho jsou některé konfigurovatelné vlastnosti k dispozici pouze pro určité typy. Následující pododdíly uvádějí vlastnosti pro každý typ úložiště.

### <a name="public-repository"></a>Veřejné úložiště

Když použijete veřejné úložiště, vaše konfigurovatelné vlastnosti jsou omezené.

Všechny konfigurovatelné vlastnosti, které se používají k nastavení veřejného úložiště Git, jsou uvedené v následující tabulce:

> [!NOTE]
> Použití spojovníku (-) k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Můžete například použít *Default-Label*, ale ne *defaultLabel*.

| Vlastnost        | Požaduje se | Příznak                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Yes    | Identifikátor URI úložiště Git, který se používá jako back-end konfiguračního serveru, začíná na *http://*, *https://*, *Git@* nebo *SSH://*. |
| `default-label` | No     | Výchozí popisek úložiště Git by měl být *název větve*, *název značky*nebo *potvrzení-ID* úložiště. |
| `search-paths`  | No     | Pole řetězců, které se používá k prohledání podadresářů úložiště Git. |

------

### <a name="private-repository-with-ssh-authentication"></a>Soukromé úložiště s ověřováním SSH

Všechny konfigurovatelné vlastnosti používané k nastavení privátního úložiště Git pomocí SSH jsou uvedené v následující tabulce:

> [!NOTE]
> Použití spojovníku (-) k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Můžete například použít *Default-Label*, ale ne *defaultLabel*.

| Vlastnost                   | Požaduje se | Příznak                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Yes    | Identifikátor URI úložiště Git použitého jako back-end konfiguračního serveru by měl být spuštěný s *http://*, *https://*, *Git@* nebo *SSH://*. |
| `default-label`            | No     | Výchozí popisek úložiště Git by měl být *název větve*, *název značky*nebo *potvrzení-ID* úložiště. |
| `search-paths`             | No     | Pole řetězců, které slouží k prohledání podadresářů úložiště Git. |
| `private-key`              | No     | Privátní klíč SSH pro přístup k úložišti Git, který je _vyžadován_ v případě, že identifikátor URI začíná na *Git@* nebo *SSH://*. |
| `host-key`                 | No     | Klíč hostitele serveru úložiště Git by neměl obsahovat předponu algoritmu, která je pokrytá `host-key-algorithm` . |
| `host-key-algorithm`       | No     | Algoritmus klíče hostitele by měl být *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*nebo *ECDSA-SHA2-nistp521*. *Požadováno* pouze v případě `host-key` , že existuje. |
| `strict-host-key-checking` | No     | Označuje, zda se instance konfiguračního serveru při využití privátního nespustí `host-key` . Hodnota by měla být *true* (výchozí hodnota) nebo *false*. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privátní úložiště s základním ověřováním

Všechny konfigurovatelné vlastnosti používané k nastavení privátního úložiště Git se základním ověřováním jsou uvedené níže.

> [!NOTE]
> Použití spojovníku (-) k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Například použijte *výchozí-Label*, nikoli *defaultLabel*.

| Vlastnost        | Požaduje se | Příznak                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Yes    | Identifikátor URI úložiště Git, který se používá jako back-end konfiguračního serveru, by měl být spuštěný pomocí *http://*, *https://*, *Git@* nebo *SSH://*. |
| `default-label` | No     | Výchozí popisek úložiště Git by měl být *název větve*, *název značky*nebo *potvrzení-ID* úložiště. |
| `search-paths`  | No     | Pole řetězců, které slouží k prohledání podadresářů úložiště Git. |
| `username`      | No     | Uživatelské jméno, které se používá pro přístup k serveru úložiště Git, se _vyžaduje_ v případě, že server úložiště Git podporuje `Http Basic Authentication` . |
| `password`      | No     | Heslo použité pro přístup k serveru úložiště Git, které se _vyžaduje_ v případě, že server úložiště Git podporuje `Http Basic Authentication` . |

> [!NOTE]
> Mnoho `Git` serverů úložiště podporuje použití tokenů místo hesel pro základní ověřování HTTP. Některá úložiště, jako je GitHub, umožňují, aby se tokeny zachovaly po neomezenou dobu. Nicméně některé servery úložiště Git, včetně Azure DevOps, vynutí vypršení platnosti tokenů za několik hodin. Úložiště, která způsobila vypršení platnosti tokenů, by neměla používat ověřování na základě tokenu u jarního cloudu Azure.

### <a name="git-repositories-with-pattern"></a>Úložiště Git se vzorem

Všechny konfigurovatelné vlastnosti používané k nastavení úložišť Git se vzorem jsou uvedené níže.

> [!NOTE]
> Použití spojovníku (-) k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Například použijte *výchozí-Label*, nikoli *defaultLabel*.

| Vlastnost                           | Požaduje se         | Příznak                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | No             | Mapa skládající se z nastavení úložiště Git se zadaným názvem. |
| `repos."uri"`                      | Ano v `repos` | Identifikátor URI úložiště Git, který se používá jako back-end konfiguračního serveru, by měl být spuštěný pomocí *http://*, *https://*, *Git@* nebo *SSH://*. |
| `repos."name"`                     | Ano v `repos` | Název, který se má identifikovat v úložišti Git, se _vyžaduje_ jenom v případě, že `repos` existuje. Například *tým-a*, *tým-B*. |
| `repos."pattern"`                  | No             | Pole řetězců používané pro shodu názvu aplikace. Pro každý vzor použijte `{application}/{profile}` formát se zástupnými znaky. |
| `repos."default-label"`            | No             | Výchozí popisek úložiště Git by měl být *název větve*, *název značky*nebo *potvrzení-ID* úložiště. |
| `repos."search-paths`"             | No             | Pole řetězců, které slouží k prohledání podadresářů úložiště Git. |
| `repos."username"`                 | No             | Uživatelské jméno, které se používá pro přístup k serveru úložiště Git, se _vyžaduje_ v případě, že server úložiště Git podporuje `Http Basic Authentication` . |
| `repos."password"`                 | No             | Heslo použité pro přístup k serveru úložiště Git, které se _vyžaduje_ v případě, že server úložiště Git podporuje `Http Basic Authentication` . |
| `repos."private-key"`              | No             | Privátní klíč SSH pro přístup k úložišti Git, který je _vyžadován_ v případě, že identifikátor URI začíná na *Git@* nebo *SSH://*. |
| `repos."host-key"`                 | No             | Klíč hostitele serveru úložiště Git by neměl obsahovat předponu algoritmu, která je pokrytá `host-key-algorithm` . |
| `repos."host-key-algorithm"`       | No             | Algoritmus klíče hostitele by měl být *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*nebo *ECDSA-SHA2-nistp521*. *Požadováno* pouze v případě `host-key` , že existuje. |
| `repos."strict-host-key-checking"` | No             | Označuje, zda se instance konfiguračního serveru při využití privátního nespustí `host-key` . Hodnota by měla být *true* (výchozí hodnota) nebo *false*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Připojení úložiště konfiguračního serveru k Azure jaře cloudu

Teď, když jsou konfigurační soubory uložené v úložišti, musíte k ní připojit jarní cloud Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Přejít na stránku s **přehledem** jarního cloudu Azure.

1. Vyberte službu, kterou chcete nakonfigurovat.

1. V levém podokně stránky služby v části **Nastavení**vyberte kartu **konfigurační server** .

![Okno konfigurační server](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Zadejte informace o úložišti přímo do Azure Portal

#### <a name="default-repository"></a>Výchozí úložiště

* **Veřejné úložiště**: v části **výchozí úložiště** v poli **URI** vložte identifikátor URI úložiště.  Nastavte **popisek** na **config**. Zajistěte, aby bylo nastavení **ověřování** **veřejné**, a pak vyberte **použít** k dokončení. 

* **Soukromé úložiště**: Azure jaře Cloud podporuje základní ověřování pomocí hesla nebo tokenu a SSH.

    * **Základní ověřování**: v části **výchozí úložiště** v poli **URI** vložte identifikátor URI úložiště a pak klikněte na tlačítko **ověřování** (ikona tužky). V podokně **Upravit ověřování** vyberte v rozevíracím seznamu **typ ověřování** možnost **http Basic**a potom zadejte své uživatelské jméno a heslo nebo token pro udělení přístupu k jarnímu cloudu Azure. Vyberte **OK**a pak vyberte **použít** k dokončení nastavování instance konfiguračního serveru.

    ![Základní ověřování podokna úprav ověřování](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Některé servery úložiště Git, jako je GitHub, pro **základní ověřování**používají *osobní tokeny* nebo *přístupový token*, jako je třeba heslo. Tento typ tokenu můžete použít jako heslo v Azure jaře cloudu, protože nikdy nevyprší platnost. Ale pro ostatní servery úložiště Git, jako je BitBucket a Azure DevOps, vyprší platnost *přístupového tokenu* v jedné nebo dvou hodinách. To znamená, že možnost nebude při použití těchto serverů úložiště u jarního cloudu Azure životaschopná.

    * **SSH**: v části **výchozí úložiště** v poli **URI** vložte identifikátor URI úložiště a pak klikněte na tlačítko **ověřování** (ikona tužky). V podokně **Upravit ověřování** vyberte v rozevíracím seznamu **typ ověřování** možnost **SSH**a potom zadejte svůj **privátní klíč**. Volitelně můžete zadat **klíč hostitele** a **algoritmus hostitelského klíče**. Nezapomeňte do svého úložiště konfiguračního serveru zahrnout svůj veřejný klíč. Vyberte **OK**a pak vyberte **použít** k dokončení nastavování instance konfiguračního serveru.

    ![Ověřování SSH v podokně upravit ověřování](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Úložiště vzorků

Pokud chcete použít volitelné **úložiště vzorků** ke konfiguraci služby, zadejte **identifikátor URI** a **ověřování** stejným způsobem jako **výchozí úložiště**. Nezapomeňte zadat **název** pro váš vzor a pak kliknutím na **použít** ho připojte k instanci. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Zadejte informace o úložišti do souboru YAML.

Pokud jste napsali soubor YAML s nastavením úložiště, můžete ho naimportovat přímo z místního počítače do jarního cloudu Azure. Jednoduchý soubor YAML pro privátní úložiště se základním ověřováním by vypadal takto:

```yaml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Vyberte tlačítko **importovat nastavení** a potom vyberte soubor YAML z adresáře projektu. Vyberte **importovat**a potom `async` se v **oznámení** zobrazí operace z vaší nabídky. Po 1-2 minutách by se mělo ohlásit úspěch.

![Podokno oznámení konfiguračního serveru](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Informace ze souboru YAML by měly být zobrazeny v Azure Portal. Vyberte **použít** k dokončení. 

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>Použití Azure Repos pro konfiguraci jarního cloudu Azure

Azure jaře Cloud má přístup k úložištím Git, které jsou veřejné, zabezpečené pomocí SSH, nebo zabezpečené pomocí ověřování HTTP Basic. Použijeme tuto poslední možnost, protože je snazší vytvořit a spravovat pomocí Azure Repos.

### <a name="get-repo-url-and-credentials"></a>Získat adresu URL a přihlašovací údaje úložiště
1. Na portálu Azure Repos pro váš projekt klikněte na tlačítko klonovat:

    ![Tlačítko klonování](media/spring-cloud-tutorial-config-server/clone-button.png)

1. Zkopírujte adresu URL klonu z textového pole. Tato adresa URL bude obvykle ve formátu:

    ```Text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Odeberte vše po `https://` a před `dev.azure.com` , včetně `@` . Výsledná adresa URL by měla být ve tvaru:

    ```Text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Tuto adresu URL uložte pro použití v další části.

1. Klikněte na generovat přihlašovací údaje Gitu. Zobrazí se uživatelské jméno a heslo. Uložte je pro použití v další části.


### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>Konfigurace přístupu Azure Spring Cloudu k úložišti Git

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Přejít na stránku s **přehledem** jarního cloudu Azure.

1. Vyberte službu, kterou chcete nakonfigurovat.

1. V levém podokně stránky služby v části **Nastavení**vyberte kartu **konfigurační server** . Nakonfigurujte úložiště, které jsme vytvořili dříve:
   - Přidejte adresu URL úložiště, kterou jste uložili z předchozí části.
   - Klikněte na `Authentication` a vyberte `HTTP Basic`
   - __Uživatelské jméno__ je uživatelské jméno uložené z předchozí části.
   - __Heslo__ je heslo uložené z předchozí části.
   - Klikněte na Apply (použít) a počkejte na úspěšné provedení operace.

   ![Spring Cloud Config Server](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-app-configuration"></a>Odstranění konfigurace aplikace

Po uložení konfiguračního souboru se na kartě **Konfigurace** zobrazí tlačítko **Odstranit konfiguraci aplikace** . Po výběru tohoto tlačítka se stávající nastavení zcela smaže. Pokud chcete instanci konfiguračního serveru připojit k jinému zdroji, jako je třeba přesun z GitHubu do Azure DevOps, měli byste ho vybrat.



## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak povolit a nakonfigurovat instanci jarního cloudového serveru pro konfiguraci. Další informace o správě aplikace najdete v tématu [škálování aplikace v Azure jarním cloudu](spring-cloud-tutorial-scale-manual.md).
