---
title: Nastavení konfiguračního serveru v Azure jaře cloudu | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nastavit jarní cloudový konfigurační server pro váš jarní cloud Azure na Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: 31ef82976a1c6938ae0bf591b2f8c8b1a0040466
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72928944"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Kurz: nastavení jarního cloudového konfiguračního serveru pro vaši službu

V tomto kurzu se dozvíte, jak připojit jaře cloudový konfigurační server k vaší jarní cloudové službě Azure.

Jarní cloudová konfigurace poskytuje podporu pro externou konfiguraci v distribuovaném systému na straně serveru a na straně klienta. Pomocí konfiguračního serveru máte centrální místo pro správu externích vlastností pro aplikace napříč všemi prostředími. Další informace najdete v referenčních informacích k [serveru pro konfiguraci jarního cloudu](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Předpoklady
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 
* Již zřízené a běžící cloudová služba Azure.  Po dokončení [tohoto rychlého](spring-cloud-quickstart-launch-app-cli.md) startu můžete zřídit a spustit cloudovou službu Azure na jaře.

## <a name="restriction"></a>Omezení

K dispozici jsou určitá omezení, pokud použijete __konfigurační server__ s back-end serverem Git. Některé vlastnosti budou automaticky vloženy do prostředí aplikace pro přístup ke __konfiguračnímu serveru__ a __zjišťování služby__. Pokud tyto vlastnosti nakonfigurujete i ze souborů **konfiguračního serveru** , může docházet ke konfliktům a neočekávanému chování. Mezi vlastnosti patří: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> Důrazně __doporučujeme, abyste výše__ uvedené vlastnosti neumístili do souborů aplikace __konfiguračního serveru__ .

## <a name="create-your-config-server-files"></a>Vytvoření souborů konfiguračního serveru

Azure jaře Cloud podporuje Azure DevOps, GitHub, GitLab a Bitbucket pro ukládání souborů konfiguračního serveru. Po přípravě úložiště proveďte konfigurační soubory podle níže uvedených pokynů a uložte je tam.

Kromě toho jsou některé konfigurovatelné vlastnosti dostupné jenom pro některé typy. Následující pododdíly uvádějí vlastnosti pro každý typ úložiště.

### <a name="public-repository"></a>Veřejné úložiště

Při použití veřejného úložiště budou konfigurovatelné vlastnosti omezené.

Všechny konfigurovatelné vlastnosti používané k nastavení veřejného úložiště `Git` jsou uvedeny níže.

> [!NOTE]
> Použití spojovníku ("-") k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Použijte například `default-label` not `defaultLabel`.

| Vlastnost        | Požaduje se | Funkce                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | `uri` úložiště `Git` používaného jako back-endu konfiguračního serveru by se mělo spustit s `http://`, `https://`, `git@`nebo `ssh://`. |
| `default-label` | `no`     | Výchozí popisek úložiště `Git` by měl být `branch name`, `tag name`nebo `commit-id` úložiště. |
| `search-paths`  | `no`     | Pole řetězců sloužících k prohledání podadresářů úložiště `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Soukromé úložiště s ověřováním SSH

Všechny konfigurovatelné vlastnosti používané k nastavení privátního úložiště `Git` s `Ssh` jsou uvedeny níže.

> [!NOTE]
> Použití spojovníku ("-") k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Použijte například `default-label` not `defaultLabel`.

| Vlastnost                   | Požaduje se | Funkce                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | `uri` úložiště `Git` používaného jako back-endu konfiguračního serveru by se mělo spustit s `http://`, `https://`, `git@`nebo `ssh://`. |
| `default-label`            | `no`     | Výchozí popisek úložiště `Git` by měl být `branch name`, `tag name`nebo `commit-id` úložiště. |
| `search-paths`             | `no`     | Pole řetězců sloužících k prohledání podadresářů úložiště `Git`. |
| `private-key`              | `no`     | `Ssh` privátní klíč pro přístup k úložišti `Git` __vyžadováno__ , když `uri` začíná `git@` nebo `ssh://`. |
| `host-key`                 | `no`     | Klíč hostitele serveru úložiště Git by neměl obsahovat předponu algoritmu, která je pokrytá `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | Algoritmus klíče hostitele by měl být `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`nebo `ecdsa-sha2-nistp521`. Vyžaduje se jenom v případě, že `host-key` existuje. |
| `strict-host-key-checking` | `no`     | Určuje, zda se má při využití privátního `host-key`spustit konfigurační server. Měla by být `true` (výchozí hodnota) nebo `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privátní úložiště s základním ověřováním

Všechny konfigurovatelné vlastnosti používané k nastavení privátního úložiště Git se základním ověřováním jsou uvedené níže.

> [!NOTE]
> Použití spojovníku ("-") k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Použijte například `default-label` not `defaultLabel`.

| Vlastnost        | Požaduje se | Funkce                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | `uri` úložiště `Git` používaného jako back-endu konfiguračního serveru by se mělo spustit s `http://`, `https://`, `git@`nebo `ssh://`. |
| `default-label` | `no`     | Výchozí popisek úložiště `Git` by měl být `branch name`, `tag name`nebo `commit-id` úložiště. |
| `search-paths`  | `no`     | Pole řetězců sloužících k prohledání podadresářů úložiště `Git`. |
| `username`      | `no`     | `username`, která se používá pro přístup k serveru úložiště `Git`, se __vyžaduje__ v případě, že server úložiště `Git` podporuje `Http Basic Authentication`. |
| `password`      | `no`     | Heslo použité pro přístup k serveru úložiště `Git`, který je __vyžadován__ , pokud server úložiště `Git` podporuje `Http Basic Authentication`. |

> [!NOTE]
> Některé servery úložiště `Git`, jako je GitHub, podporují jako heslo pro `HTTP Basic Authentication` "osobní token" nebo "Access-token". Tento typ tokenu můžete použít také jako heslo a na token "Personal-token" nebo "Access-token" nebude vypršet. U serverů úložiště Git, jako je BitBucket a Azure DevOps, vyprší platnost tokenu za jednu nebo dvě hodiny, takže tato možnost nebude životaschopná pro použití se službou Azure Pramenitého cloudu.

### <a name="git-repositories-with-pattern"></a>Úložiště Git se vzorem

Všechny konfigurovatelné vlastnosti používané k nastavení úložišť Git se vzorem jsou uvedené níže.

> [!NOTE]
> Použití spojovníku ("-") k oddělení slov je jediná konvence pojmenování, která je aktuálně podporována. Použijte například `default-label` not `defaultLabel`.

| Vlastnost                           | Požaduje se         | Funkce                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Mapa skládající se z nastavení úložiště `Git` se zadaným názvem. |
| `repos."uri"`                      | `yes` v `repos` | `uri` úložiště `Git` používaného jako back-endu konfiguračního serveru by se mělo spustit s `http://`, `https://`, `git@`nebo `ssh://`. |
| `repos."name"`                     | `yes` v `repos` | Název pro identifikaci jednoho úložiště `Git` __požadovaný__ pouze v případě, že `repos` existuje. Například z výše uvedeného `team-A` `team-B`. |
| `repos."pattern"`                  | `no`             | Pole řetězců používané pro shodu názvu aplikace. Pro každý vzor použijte `{application}/{profile}` formátu se zástupnými znaky. |
| `repos."default-label"`            | `no`             | Výchozí popisek úložiště `Git` by měl být `branch name`, `tag name`nebo `commit-id` úložiště. |
| `repos."search-paths` "             | `no`             | Pole řetězců sloužících k prohledání podadresářů úložiště `Git`. |
| `repos."username"`                 | `no`             | `username`, která se používá pro přístup k serveru úložiště `Git`, se __vyžaduje__ v případě, že server úložiště `Git` podporuje `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Heslo použité pro přístup k serveru úložiště `Git`, který je __vyžadován__ , pokud server úložiště `Git` podporuje `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | `Ssh` privátní klíč pro přístup k úložišti `Git` se __vyžaduje__ , když `uri` začíná `git@` nebo `ssh://`. |
| `repos."host-key"`                 | `no`             | Klíč hostitele serveru úložiště Git by neměl obsahovat předponu algoritmu, která je pokrytá `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | Algoritmus klíče hostitele by měl být `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`nebo `ecdsa-sha2-nistp521`. __Vyžaduje__ se jenom v případě, že existuje `host-key`. |
| `repos."strict-host-key-checking"` | `no`             | Určuje, zda se má při využití privátního `host-key`spustit konfigurační server. Měla by být `true` (výchozí hodnota) nebo `false`. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>Importovat soubor `application.yml` ze jarní konfigurace cloudu

Můžete importovat některá výchozí nastavení serveru konfigurace přímo z webu [jarní konfigurace cloudu](https://spring.io/projects/spring-cloud-config) . Můžete to provést přímo z Azure Portal, takže nemusíte nyní provádět žádné kroky k přípravě souborů nebo úložiště konfiguračního serveru.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Připojení úložiště konfiguračního serveru k Azure jaře cloudu

Teď, když máte konfigurační soubory uložené v úložišti, musíte k ní připojit jarní cloud Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Přejděte na stránku s **přehledem** jarního cloudu Azure.

1. Přejděte na kartu **konfigurační server** v záhlaví **Nastavení** v nabídce na levé straně.

### <a name="public-repository"></a>Veřejné úložiště

Pokud je vaše úložiště veřejné, stačí kliknout na tlačítko **veřejné** a vložit adresu URL.

### <a name="private-repository"></a>Soukromé úložiště

Azure jarní Cloud podporuje ověřování SSH. Pokud chcete přidat veřejný klíč do úložiště, postupujte podle pokynů v Azure Portal. Pak nezapomeňte do konfiguračního souboru zahrnout svůj privátní klíč.

Kliknutím na **použít** dokončete nastavování konfiguračního serveru.

## <a name="delete-your-app-configuration"></a>Odstranění konfigurace aplikace

Po uložení konfiguračního souboru se na kartě **Konfigurace** zobrazí tlačítko **Odstranit konfiguraci aplikace** . Tím se úplně smaže stávající nastavení. To byste měli udělat, pokud chcete připojit konfigurační server k jinému zdroji, například přesun z GitHubu do Azure DevOps.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak povolit a nakonfigurovat konfigurační server. Další informace o správě aplikace najdete v kurzu ručního škálování aplikace.

> [!div class="nextstepaction"]
> [Naučte se, jak ručně škálovat cloudovou aplikaci Azure na jaře](spring-cloud-tutorial-scale-manual.md).

