---
title: Nastavení serveru GitHub Enterprise v privátním cloudu řešení Azure VMware
description: Přečtěte si, jak nastavit server GitHub Enterprise v privátním cloudu řešení Azure VMware.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: afce212416c7c12631a7f8d388dc991ed957736f
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949305"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Nastavení serveru GitHub Enterprise v privátním cloudu řešení Azure VMware

V tomto článku Vás provedeme kroky pro nastavení serveru GitHub Enterprise Server, "místní" verze [GitHub.com](https://github.com/)v privátním cloudu řešení Azure VMware. Scénář popsaný v tomto příkladu je určen pro instanci serveru GitHub Enterprise, která podporuje až 3 000 vývojářů na akcích GitHubu s podporou až 25 úloh za minutu. Zahrnuje nastavení (v době psaní) funkcí ve *verzi Preview* , například akce GitHubu. Pokud chcete nastavení přizpůsobit konkrétním potřebám, Projděte si požadavky uvedené v části [instalace serveru GitHub Enterprise Server na VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Než začnete

Server GitHub Enterprise vyžaduje platný licenční klíč. Můžete si zaregistrovat [zkušební licenci](https://enterprise.github.com/trial). Pokud chcete, aby se funkce GitHub Enterprise Server rozšířily prostřednictvím integrace, můžete získat nárok na bezplatnou bezplatnou licenci pro vývojáře. Požádat o tuto licenci prostřednictvím [partnerského programu GitHubu](https://partner.github.com/).

## <a name="installing-github-enterprise-server-on-vmware"></a>Instalace GitHub Enterprise serveru na VMware

Stáhněte si [aktuální vydání GitHub Enterprise serveru](https://enterprise.github.com/releases/2.19.0/download) pro VMware ESXi/VSPHERE (vajíčka) a [Nasaďte šablonu vajíček](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) , kterou jste si stáhli.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu." (akce) |
| --- | --- | --- |
| Virtuální procesory | 4 | 8 |
| Paměť | 32 GB | 61 GB |
| Připojené úložiště | 250 GB | 300 GB |
| Kořenové úložiště | 200 GB | 200 GB |

Vaše potřeby se ale můžou lišit. Pokyny k hardwarovým hlediskům najdete v tématu [instalace serveru GitHub Enterprise Server na VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Další informace najdete v tématu [Přidání prostředků procesoru nebo paměti pro VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) pro přizpůsobení konfigurace hardwaru podle vaší situace.

## <a name="configuring-the-github-enterprise-server-instance"></a>Konfigurace instance serveru GitHub Enterprise Server

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::  

Jakmile se nově zřízený virtuální počítač zapne, [nakonfigurujte ho přes prohlížeč](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Bude nutné nahrát soubor s licencí a nastavit heslo konzoly pro správu. Nezapomeňte si toto heslo zapsat někam do trezoru.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::    

Doporučujeme, abyste aspoň tyto kroky provedli:

1. Nahrajte veřejný klíč SSH do konzoly pro správu, abyste měli [k prostředí pro správu přístup přes SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. [Nakonfigurujte u své instance TLS](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) , abyste mohli používat certifikát, který je podepsaný důvěryhodnou certifikační autoritou.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Použijte nastavení.  Během restartování instance můžete pokračovat v dalším kroku a **nakonfigurovat BLOB Storage pro akce GitHubu**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Po restartování instance vytvořte v instanci nový účet správce. Nezapomeňte si poznamenat také heslo tohoto uživatele.

### <a name="additional-configuration-steps"></a>Další kroky konfigurace

Pro posílení vaší instance pro produkční použití jsou doporučeny tyto volitelné kroky při instalaci:

1. Konfigurace [vysoké dostupnosti](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) pro ochranu proti:

    - Zhroucení softwaru (na úrovni operačního systému nebo aplikace)
    - Selhání hardwaru (úložiště, procesor, paměť RAM atd.)
    - Selhání systému hostitele virtualizace
    - Logicky nebo fyzicky závažná síť

2. [Nakonfigurujte](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [Nástroje pro zálohování](https://github.com/github/backup-utils), které poskytují snímky se správou verzí pro zotavení po havárii, hostované v dostupnosti, která je oddělená od primární instance.
3. [Nastavením izolace subdomény](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)pomocí platného certifikátu TLS můžete zmírnit skriptování mezi weby a další související chyby zabezpečení.

## <a name="configuring-blob-storage-for-github-actions"></a>Konfigurace úložiště objektů BLOB pro akce GitHubu

> [!NOTE]
> Akce GitHubu jsou [v tuto chvíli dostupné jako omezená beta verze na webu GitHub Enterprise Server verze 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Externí úložiště objektů BLOB je potřeba k povolení akcí GitHubu na serveru GitHub Enterprise (aktuálně dostupného jako "beta" funkce). Toto externí úložiště objektů BLOB se používá v akcích k ukládání artefaktů a protokolů. Akce na serveru GitHub Enterprise Server [podporují službu Azure Blob Storage jako poskytovatele úložiště](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (a některé jiné). Proto zřídíme nový účet Azure Storage s [typem účtu úložiště](../storage/common/storage-account-overview.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json#types-of-storage-accounts) BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Po dokončení nasazení nového prostředku BlobStorage zkopírujte a poznamenejte si připojovací řetězec (k dispozici v přístupových klíčích). Brzy budete potřebovat tento řetězec.

## <a name="setting-up-the-github-actions-runner"></a>Nastavení spouštěče akcí GitHubu

> [!NOTE]
> Akce GitHubu jsou [v tuto chvíli dostupné jako omezená beta verze na webu GitHub Enterprise Server verze 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

V tomto okamžiku byste měli mít spuštěnou instanci serveru GitHub Enterprise, přičemž byl vytvořen účet správce. Měli byste také mít externí úložiště objektů blob, které akce GitHubu použijí k trvalému uložení.

Teď vytvoříme někde pro akce GitHubu, které se mají spustit; znovu použijeme řešení Azure VMware.

Nejdřív se zřídí nový virtuální počítač v clusteru. Náš virtuální počítač založíme na [nejnovější verzi Ubuntu serveru](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Až se virtuální počítač vytvoří, zapněte ho a připojte se k němu přes SSH.

Dále nainstalujte aplikaci [Actions Runner](https://github.com/actions/runner) , která spustí úlohu z pracovního postupu akce GitHubu. Identifikujte a stáhněte nejaktuálnější verzi systému Linux x64 sady akcí, která se nachází na [stránce vydané verze](https://github.com/actions/runner/releases) , nebo spuštěním následujícího rychlého skriptu. Tento skript vyžaduje, aby ve vašem VIRTUÁLNÍm počítači byl k dispozici jak složené, tak [JQ](https://stedolan.github.io/jq/) .

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Teď byste měli mít soubor místně na vašem VIRTUÁLNÍm počítači, Actions-Runner-Linux-arm64- \* . tar. gz. Extrahovat tento tarballu místně:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Toto extrakce rozbalí několik souborů místně, včetně `config.sh` `run.sh` skriptu a, který se vrátí za chvíli.

## <a name="enabling-github-actions"></a>Povolení akcí GitHubu

> [!NOTE]
> Akce GitHubu jsou [v tuto chvíli dostupné jako omezená beta verze na webu GitHub Enterprise Server verze 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Skoro tam! Pojďme nakonfigurovat a povolit akce GitHubu v instanci serveru GitHub Enterprise. K [prostředí GitHubu pro instanci podnikového serveru bude potřeba přistupovat přes SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)a pak spustit následující příkazy:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Další spuštění:

`ghe-actions-check -s blob`

Měl by se zobrazit výstup: "Blob Storage je v pořádku.

Teď, když jsou nakonfigurované akce GitHubu, povolte ji pro vaše uživatele. Přihlaste se k instanci serveru GitHub Enterprise Server jako správce a vyberte ![ ikonu rakety.](media/github-enterprise-server/rocket-icon.png) v pravém horním rohu libovolné stránky. Na levém bočním panelu vyberte **Přehled Enterprise**, pak **zásady**, **Akce**a vyberte možnost pro **povolení akcí pro všechny organizace**.

V dalším kroku nakonfigurujte Runner z karty **spouštěče pro samoobslužné hostování** . V rozevíracím seznamu vyberte **Přidat nový** a potom **Nový Runner** .

Na další stránce budete mít k dispozici sadu příkazů, které se mají spustit. potřebujeme jenom zkopírovat příkaz pro **konfiguraci** spouštěče, např.:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Zkopírujte `config.sh` příkaz a vložte ho do relace v rámci spouštěče akcí (dříve vytvořené).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Ke *spuštění* spouštěče použijte příkaz run.sh:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Pokud chcete, aby byl tento spouštěč dostupný pro organizace v podniku, upravte jeho přístup k organizaci:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

V tomto případě je zpřístupníme pro všechny organizace, ale můžete také omezit přístup k podmnožině organizací a dokonce i k určitým úložištím.

## <a name="optional-configuring-github-connect"></a>Volitelné Konfiguruje se připojení GitHubu.

I když je tento krok nepovinný, doporučujeme ho, pokud máte v plánu využívat akce open source, které jsou dostupné na GitHub.com. To vám umožní sestavovat práci dalších uživatelů pomocí odkazů na tyto opakovaně použitelné akce v pracovních postupech.

Pokud chcete povolit GitHub Connect, postupujte podle kroků v části [Povolení automatického přístupu k akcím GitHub.com pomocí GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect).

Jakmile je připojení GitHubu povolené, vyberte **Server, který se má použít v GitHub.com ve spuštění pracovního postupu** .

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Nastavení a spuštění prvního pracovního postupu

Teď jsou akce a připojení GitHubu nastavené, takže tuto práci budeme dát k dobrému používání. Tady je příklad pracovního postupu, který odkazuje na vynikající [octokit/Request-Action](https://github.com/octokit/request-action), což nám umožňuje "skript" GitHubu prostřednictvím interakcí pomocí rozhraní API GitHubu, které využívá akce GitHubu.

V tomto základním pracovním postupu budeme používat `octokit/request-action` jenom k otevření problému na GitHubu pomocí rozhraní API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

>[!NOTE]
>GitHub.com hostuje akci, ale když ji spustí na serveru GitHub Enterprise, *automaticky* použije rozhraní API serveru GitHub Enterprise.

Pokud se rozhodnete, že připojení GitHubu nepovolíte, můžete použít následující alternativní pracovní postup.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Přejděte do úložiště v instanci a přidejte výše uvedený pracovní postup jako: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Na kartě **Akce** pro vaše úložiště počkejte, než se pracovní postup spustí.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Můžete ho také sledovat pomocí spouštěče.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Pokud vše úspěšně proběhlo, měli byste v úložišti zobrazit nový problém s názvem Hello World.

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Vyberte, chcete-li spustit GitHub místně nebo v cloudu.":::

Blahopřejeme! Právě jste dokončili pracovní postup prvního pracovního postupu na serveru GitHub Enterprise, který běží na vašem privátním cloudu řešení Azure VMware.

Právě dosazujeme plochu toho, co můžete dělat s akcemi na GitHubu. Další inspiraci najdete v seznamu akcí na [webu GitHub](https://github.com/marketplace)nebo si [vytvořte vlastní](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Další kroky

V tomto článku jsme na svém privátním cloudu vašeho řešení Azure VMware nastavili novou instanci serveru GitHub Enterprise, která je v místním prostředí rovnocenná GitHub.com. Tato instance zahrnuje podporu pro akce GitHubu a používá službu Azure Blob Storage k zajištění Persistence protokolů a artefaktů. Jedná se o skvělou kombinaci pro moderní, týmovou a bezpečnostní prostředí pro vývoj softwaru. Vychází z pevného základu řešení Azure VMware, což vám umožní využívat cloudové prostředky ve známém nastavení.

Další informace najdete v následujících zdrojích informací:

- [Začínáme s akcemi GitHubu](https://docs.github.com/en/actions)
- [Zapojit se do programu beta verze](https://resources.github.com/beta-signup/)
- [Další informace o správě serveru GitHub Enterprise](https://githubtraining.github.io/admin-training/#/00_getting_started)