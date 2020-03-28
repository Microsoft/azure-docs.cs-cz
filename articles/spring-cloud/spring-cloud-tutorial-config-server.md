---
title: Kurz – nastavení instance konfiguračního serveru v Azure Spring Cloud
description: V tomto kurzu se dozvíte, jak nastavit instanci spring cloudového konfiguračního serveru pro azure spring cloud na webu Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.openlocfilehash: 5e0b5633a153583117cfe0d90ec5c0e7c5f2a147
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277533"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Kurz: Nastavení instance serveru Spring Cloud Config Server pro vaši službu

Tento článek ukazuje, jak připojit instanci spring cloudového konfiguračního serveru ke službě Azure Spring Cloud.

Spring Cloud Config poskytuje serverovou a klientskou podporu pro externalizovanou konfiguraci v distribuovaném systému. S instancí Config Server máte centrální místo pro správu externích vlastností pro aplikace ve všech prostředích. Další informace naleznete v [tématu Spring Cloud Config Server reference](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 
* Již zřízené a spuštěné služby Azure Spring Cloud. Pokud chcete nastavit a spustit službu Azure Spring Cloud, přečtěte si [úvodní příručku: Spuštění aplikace Java Spring pomocí azure cli](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Omezení

Existují určitá omezení při použití config serveru s git back-endem. Některé vlastnosti jsou automaticky vloženy do prostředí aplikace pro přístup k serveru Konfigurace a zjišťování služeb. Pokud tyto vlastnosti nakonfigurujete také ze souborů serveru Konfigurace, může dojít ke konfliktům a neočekávanému chování. Vlastnosti zahrnují: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> Důrazně doporučujeme, abyste _výše_ uvedené vlastnosti neumisteji do souborů aplikace Config Server.

## <a name="create-your-config-server-files"></a>Vytvoření souborů serveru Config Server

Azure Spring Cloud podporuje Azure DevOps, GitHub, GitLab a Bitbucket pro ukládání souborů config serveru. Až budete mít úložiště připravené, vytvořte konfigurační soubory s následujícími pokyny a uložte je tam.

Kromě toho některé konfigurovatelné vlastnosti jsou k dispozici pouze pro určité typy. V následujících podčástech jsou uvedeny vlastnosti pro každý typ úložiště.

### <a name="public-repository"></a>Veřejné úložiště

Při použití veřejného úložiště jsou konfigurovatelné vlastnosti omezenější.

Všechny konfigurovatelné vlastnosti, které se používají k nastavení veřejného úložiště Git, jsou uvedeny v následující tabulce:

> [!NOTE]
> Použití pomlčky (-) k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Můžete například použít *výchozí popisek*, ale ne *výchozí popisek*.

| Vlastnost        | Požaduje se | Funkce                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ano    | Identifikátor URI úložiště Git, který se používá jako back-end konfiguračního serveru, začíná *http://*, *https://*, *git@* nebo *ssh://*. |
| `default-label` | Ne     | Výchozí popisek úložiště Git by měl být *název větve*, *název značky*nebo *id potvrzení* úložiště. |
| `search-paths`  | Ne     | Pole řetězců, které se používají k vyhledávání podadresářů úložiště Git. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privátní úložiště s ověřováním SSH

Všechny konfigurovatelné vlastnosti používané k nastavení soukromého úložiště Git s SSH jsou uvedeny v následující tabulce:

> [!NOTE]
> Použití pomlčky (-) k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Můžete například použít *výchozí popisek*, ale ne *výchozí popisek*.

| Vlastnost                   | Požaduje se | Funkce                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Ano    | Identifikátor URI úložiště Git používaného jako back-end konfiguračního serveru by měl být spuštěn s *http://*, *https://*, *git@* nebo *ssh://*. |
| `default-label`            | Ne     | Výchozí popisek úložiště Git by měl být *název větve*, *název značky*nebo *id potvrzení* úložiště. |
| `search-paths`             | Ne     | Pole řetězců používané k vyhledávání podadresářů úložiště Git. |
| `private-key`              | Ne     | Soukromý klíč SSH pro přístup k úložišti Git, _který je vyžadován_ při spuštění identifikátoru URI *git@* nebo *ssh://*. |
| `host-key`                 | Ne     | Klíč hostitele serveru úložiště Git by neměl obsahovat předponu `host-key-algorithm`algoritmu, která je pokryta . |
| `host-key-algorithm`       | Ne     | Algoritmus klíče hostitele by měl být *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*nebo *ecdsa-sha2-nistp521*. *Povinné* pouze `host-key` v případě, že existuje. |
| `strict-host-key-checking` | Ne     | Označuje, zda se instance serveru Config server `host-key`nespustí při využití privátního . By měla být *true* (výchozí hodnota) nebo *false*. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privátní úložiště se základním ověřováním

Všechny konfigurovatelné vlastnosti používané k nastavení soukromého úložiště Git se základním ověřováním jsou uvedeny níže.

> [!NOTE]
> Použití pomlčky (-) k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Použijte například *výchozí popisek*, nikoli *výchozí popisek*.

| Vlastnost        | Požaduje se | Funkce                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ano    | Identifikátor URI úložiště Git, který se používá jako back-end konfiguračního serveru, by měl být spuštěn s *http://*, *https://*, *git@* nebo *ssh://*. |
| `default-label` | Ne     | Výchozí popisek úložiště Git by měl být *název větve*, *název značky*nebo *id potvrzení* úložiště. |
| `search-paths`  | Ne     | Pole řetězců používané k vyhledávání podadresářů úložiště Git. |
| `username`      | Ne     | Uživatelské jméno, které se používá pro přístup k serveru úložiště Git, `Http Basic Authentication` _povinné,_ když server úložiště Git podporuje . |
| `password`      | Ne     | Heslo použité pro přístup k serveru úložiště Git, které `Http Basic Authentication`je _vyžadováno_ v případě, že server úložiště Git podporuje . |

> [!NOTE]
> Mnoho `Git` serverů úložiště podporuje použití tokenů spíše než hesla pro základní ověřování HTTP. Některá úložiště, jako je například GitHub, umožňují, aby tokeny přetrvávaly po neomezenou dobu. Některé servery úložiště Git, včetně Azure DevOps, však vynutit platnost tokeny vyprší během několika hodin. Úložiště, která způsobí vypršení platnosti tokenů, by neměla používat ověřování na základě tokenů s Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Úložiště Git se vzorem

Všechny konfigurovatelné vlastnosti používané k nastavení úložišť Git se vzorem jsou uvedeny níže.

> [!NOTE]
> Použití pomlčky (-) k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Použijte například *výchozí popisek*, nikoli *výchozí popisek*.

| Vlastnost                           | Požaduje se         | Funkce                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Ne             | Mapa skládající se z nastavení úložiště Git s daným názvem. |
| `repos."uri"`                      | Ano na`repos` | Identifikátor URI úložiště Git, který se používá jako back-end konfiguračního serveru, by měl být spuštěn s *http://*, *https://*, *git@* nebo *ssh://*. |
| `repos."name"`                     | Ano na`repos` | Název k identifikaci v úložišti Git, _povinný_ pouze v případě, `repos` že existuje. Například *tým A*, *team-B*. |
| `repos."pattern"`                  | Ne             | Pole řetězců, které se používají k porovnání názvu aplikace. Pro každý vzor `{application}/{profile}` použijte formát se zástupnými znaky. |
| `repos."default-label"`            | Ne             | Výchozí popisek úložiště Git by měl být *název větve*, *název značky*nebo *id potvrzení* úložiště. |
| `repos."search-paths`"             | Ne             | Pole řetězců používané k vyhledávání podadresářů úložiště Git. |
| `repos."username"`                 | Ne             | Uživatelské jméno, které se používá pro přístup k serveru úložiště Git, `Http Basic Authentication` _povinné,_ když server úložiště Git podporuje . |
| `repos."password"`                 | Ne             | Heslo použité pro přístup k serveru úložiště Git, které `Http Basic Authentication`je _vyžadováno_ v případě, že server úložiště Git podporuje . |
| `repos."private-key"`              | Ne             | Soukromý klíč SSH pro přístup k úložišti Git, _který je vyžadován_ při spuštění identifikátoru URI *git@* nebo *ssh://*. |
| `repos."host-key"`                 | Ne             | Klíč hostitele serveru úložiště Git by neměl obsahovat předponu `host-key-algorithm`algoritmu, která je pokryta . |
| `repos."host-key-algorithm"`       | Ne             | Algoritmus klíče hostitele by měl být *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*nebo *ecdsa-sha2-nistp521*. *Povinné* pouze `host-key` v případě, že existuje. |
| `repos."strict-host-key-checking"` | Ne             | Označuje, zda se instance serveru Config server `host-key`nespustí při využití privátního . By měla být *true* (výchozí hodnota) nebo *false*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Připojení úložiště config serveru k Azure Spring Cloud

Teď, když jsou vaše konfigurační soubory uloženy v úložišti, musíte k němu připojit Azure Spring Cloud.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Přejděte na stránku **Přehled** azure jarního cloudu.

1. Vyberte službu, kterou chcete konfigurovat.

1. V levém podokně stránky služby vyberte v části **Nastavení**kartu **Konfigurační server.**

![Okno Server konfigurace](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Zadávání informací o úložišti přímo na portál Azure

#### <a name="default-repository"></a>Výchozí úložiště

* **Veřejné úložiště**: V části **Výchozí úložiště** vložte do pole **Uri** identifikátor URI identifikátor URI.  Nastavte **popisek** na **config**. Zkontrolujte, zda je nastavení **Ověřování** **veřejné**, a pak vyberte **použít** k dokončení. 

* **Privátní úložiště**: Azure Spring Cloud podporuje základní ověřování založené na heslech/tokenech a SSH.

    * **Základní ověřování**: V části **Výchozí úložiště** vložte do pole **Uri** identifikátor URI a pak vyberte tlačítko **Ověřování** (ikona tužky). V podokně **Upravit ověřování** v rozevíracím seznamu **Typ ověřování** vyberte HTTP **Basic**a zadejte své uživatelské jméno a heslo/token, abyste udělili přístup k Azure Spring Cloud. Vyberte **OK**a pak **Apply** dokončete nastavení instance konfiguračního serveru.

    ![Podokno Upravit ověřování](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Některé servery úložiště Git, například GitHub, používají pro **základní ověřování** *osobní token* nebo *přístupový token*, například heslo . Tento druh tokenu můžete použít jako heslo ve službě Azure Spring Cloud, protože nikdy nevyprší jeho platnost. Ale pro ostatní servery úložiště Git, jako je Bitbucket a Azure DevOps, *přístupový token* vyprší za jednu nebo dvě hodiny. To znamená, že tato možnost není životaschopná, když tyto servery úložiště používáte s Azure Spring Cloud.

    * **SSH**: V části **Výchozí úložiště** vložte do pole **Uri** identifikátor URI a pak vyberte tlačítko **Ověřování** (ikona tužky). V podokně **Upravit ověřování** vyberte v rozevíracím seznamu **Typ ověřování** **položku SSH**a zadejte **svůj soukromý klíč**. Volitelně můžete zadat **klíč Host** a **algoritmus klíče hostitele**. Nezapomeňte zahrnout veřejný klíč do úložiště serveru Config Server. Vyberte **OK**a pak **Apply** dokončete nastavení instance konfiguračního serveru.

    ![Podokno Upravit ověřování](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Úložiště vzorů

Pokud chcete ke **konfiguraci služby** použít volitelné úložiště vzorů, zadejte **identifikátor URI** a **ověřování** stejným způsobem jako **výchozí úložiště**. Nezapomeňte do něj uvést **název** vzorku a pak ho kliknutím na **Použít** připojit k instanci připojit. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Zadejte informace o úložišti do souboru YAML

Pokud jste napsali soubor YAML s nastavením úložiště, můžete soubor importovat přímo z místního počítače do Azure Spring Cloud. Jednoduchý soubor YAML pro soukromé úložiště se základním ověřováním by vypadal takto:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Vyberte tlačítko **Importovat nastavení** a pak vyberte soubor YAML z adresáře projektu. Vyberte **Importovat** `async` a pak se zobrazí operace z **oznámení.** Po 1-2 minutách by měl hlásit úspěch.

![Podokno Oznámení serveru Konfigurace](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Informace ze souboru YAML by se měly zobrazit na webu Azure Portal. Vyberte **Použít** k dokončení. 


## <a name="delete-your-app-configuration"></a>Odstranění konfigurace aplikace

Po uložení konfiguračního souboru se na kartě **Konfigurace** zobrazí tlačítko **Odstranit konfigurační aplikaci.** Měli byste ji vybrat, pokud chcete připojit instanci konfiguračního serveru k jinému zdroji, jako je přechod z GitHubu do Azure DevOps.



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak povolit a nakonfigurovat instanci serveru Spring Cloud Config Server. Další informace o správě aplikace najdete v kurzu o ručním škálování aplikace.

> [!div class="nextstepaction"]
> [Kurz: Škálování aplikace v Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md)
