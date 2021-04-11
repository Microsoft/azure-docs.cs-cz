---
title: Jak spustit aplikaci s Fortanix důvěrného Computing Manageru
description: Naučte se používat Fortanix důvěrného Computing Manageru k převodu vašich kontejnerových imagí.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: JenCook
ms.openlocfilehash: 91a3f0a38d1182e445eba39bf31092be17f9a1fd
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111549"
---
# <a name="how-to-run-an-application-with-fortanix-confidential-computing-manager"></a>Postupy: spuštění aplikace s Fortanix důvěrného Computing Manageru

Spusťte aplikaci v důvěrné výpočetní službě Azure pomocí [Fortanix důvěrného Computing Manageru](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) a [agenta Fortanix](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) v [Fortanix](https://www.fortanix.com/).


Fortanix je dodavatel softwaru třetí strany s produkty a službami postavenými nad infrastrukturou Azure. Existují další poskytovatelé třetích stran nabízející podobné důvěrné výpočetní služby v Azure.

> [!Note]
> Produkty, na které se odkazuje v tomto dokumentu, nejsou pod kontrolou Microsoftu. Společnost Microsoft poskytuje tyto informace jenom jako pohodlí a odkaz na tyto produkty od jiných výrobců nepředpokládá, že Microsoft.

V tomto kurzu se dozvíte, jak převést Image aplikace na tajnou bitovou kopii chráněnou na výpočetní výkon. Toto prostředí používá [Fortanix](https://www.fortanix.com/) software, který využívá Azure DCsv2-Series virtuálních počítačů s podporou Intel SGX. Toto řešení orchestruje kritické zásady zabezpečení, jako je ověřování identity a řízení přístupu k datům.

Pro podporu specifickou pro Fortanix se připojte ke [komunitě pracovní rezervy Fortanix](https://fortanix.com/community/) a kanál použijte `#enclavemanager` .

## <a name="prerequisites"></a>Požadavky

1. Pokud nemáte účet Fortanix důvěrného Computing Manageru, před tím, než začnete, se [Zaregistrujte](https://ccm.fortanix.com/auth/sign-up) .
1. Soukromý registr [Docker](https://docs.docker.com/) pro nabízení převedených imagí aplikace
1. Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) před tím, než začnete.

> [!NOTE]
> Bezplatné zkušební účty nemají přístup k virtuálním počítačům použitým v tomto kurzu. Upgradujte prosím na předplatné s průběžnými platbami.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Přidání aplikace do Fortanix důvěrného Computing Manageru

1. Přihlaste se k [Fortanix důvěrného Computing Manageru (FORTANIX ccm)](https://ccm.fortanix.com).
1. Pokud chcete vytvořit nový účet, otevřete stránku **účty** a vyberte **Přidat účet** .

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="Snímek obrazovky, který ukazuje, jak vytvořit účet.":::

1. Po vytvoření účtu klikněte na **Vybrat** a vyberte nově vytvořený účet. Nyní můžeme zahájit registraci výpočetních uzlů a vytváření aplikací.
1. Vyberte tlačítko **+ aplikace** a přidejte aplikaci. V tomto příkladu přidáme enklávy aplikaci OS serveru.

1. Vyberte tlačítko **Přidat** pro aplikaci enklávy OS.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="Snímek obrazovky, který ukazuje, jak přidat aplikaci":::

   > [!NOTE]
   > Tento kurz se zabývá pouze přidáváním aplikací pro operační systém enklávy. [Přečtěte si další informace](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager) o tom, jak EDP aplikace Rust v Fortanix pro správce důvěrných computingů.

1. V tomto kurzu použijeme pro ukázkovou aplikaci registr Docker Fortanix. Vyplňte podrobnosti z následujících informací. Uložte výstupní Image pomocí privátního registru Docker.

    - **Název aplikace**: aplikační server Pythonu
    - **Popis**: Server na baňce Python
    - **Název vstupní image**: fortanix/Python – baňka
    - **Název výstupního obrázku**: fortanx-Private/Python-baněk-SGX
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Velikost paměti**: 1 GB
    - **Počet vláken**: 128

    *Volitelné*: Spusťte aplikaci.
    - **Docker Hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **Aplikace**: fortanix/Python – baňka

      Spusťte následující příkaz:

      ```bash
         sudo docker run fortanix/python-flask
      ```

1. Přidejte certifikát. Zadejte informace podle níže uvedených podrobností a potom vyberte **Další**:
    - **Doména**: MyApp. domain. Dom
    - **Typ**: certifikát vydaný správcem důvěrných computingů
    - **Cesta ke klíči**:/appkey.pem
    - **Typ klíče**: RSA
    - **Cesta k certifikátu**:/appcert.pem
    - **Velikost klíče RSA**: 2048 bitů

## <a name="create-an-image"></a>Vytvoření image

Fortanix image CCM je softwarová verze nebo verze aplikace. Každý obrázek je přidružený k jednomu enklávy hash (MRENCLAVE).

1. Na stránce **Přidat obrázek** zadejte **pověření registru** pro **název výstupního obrázku**. Tyto přihlašovací údaje se používají pro přístup k privátnímu registru Docker, ve kterém se obrázek zobrazí.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="Snímek obrazovky, který ukazuje, jak vytvořit obrázek.":::

1. Zadejte značku obrázku a vyberte **vytvořit**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-tag.png" alt-text="Snímek obrazovky, který ukazuje, jak přidat značku.":::

## <a name="domain-and-image-allowlist"></a>Povolených domén a imagí

Aplikace, jejíž doména se přidá do služby povolených, získá certifikát TLS od správce důvěrného computingu Fortanix. Podobně platí, že když se aplikace spustí z převedeného obrazu, pokusí se kontaktovat Fortanix důvěrného Computing Manager. Aplikace pak bude žádat o certifikát TLS.

Přepněte na kartu **úlohy** na levé straně a schvalte nedokončené žádosti, aby se povolila doména a image.

## <a name="enroll-compute-node-agent-in-azure"></a>Registrace agenta výpočetního uzlu v Azure

### <a name="generate-and-copy-join-token"></a>Generovat a kopírovat token JOIN

Ve Správci Fortanix důvěrného computingu vytvoříte token. Tento token umožňuje výpočetnímu uzlu v Azure ověřit si sám sebe. Tento token budete muset dát k virtuálnímu počítači Azure.

1. V konzole pro správu vyberte tlačítko **+ zaregistrovat uzel** .
1. Vyberte **Generovat token** pro vygenerování tokenu JOIN. Zkopírujte token.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Registrace uzlů do agenta uzlu Fortanix v Azure Marketplace

Když se vytvoří agent uzlu Fortanix, nasadí se virtuální počítač, síťové rozhraní, virtuální síť, skupina zabezpečení sítě a veřejná IP adresa do vaší skupiny prostředků Azure. Vaše předplatné Azure se bude fakturovat po hodinách pro virtuální počítač. Než vytvoříte agenta uzlu Fortanix, přečtěte si stránku s [cenami virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Azure pro DCsv2-Series. Odstraňte prostředky Azure, pokud se nepoužívají.

1. Přejít na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) a přihlaste se pomocí přihlašovacích údajů Azure.
1. Na panelu hledání zadejte Fortanix pro **důvěrné výpočetní uzly**. Vyberte aplikaci, která se zobrazí v poli pro hledání s názvem **Fortanix důvěrné výpočetní uzly** , aby se mohla přejít na domovskou stránku nabídky.

   ![Hledat na Marketplace](media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png)
1. Vyberte **získat hned**, v případě potřeby vyplňte svoje informace a vyberte **pokračovat**. Budete přesměrováni na Azure Portal.
1. Vyberte **vytvořit** a zadejte stránku nasazení agenta Fortanix důvěrného výpočetního uzlu.
1. Na této stránce budete zadávat informace pro nasazení virtuálního počítače. Konkrétně tento virtuální počítač je DCsv2-Series virtuální počítač s podporou Intel SGX z Azure s nainstalovaným softwarem agenta Fortanix Node. Agent uzlu umožní, aby se převedená image bezpečně spouštěla na uzlech Intel SGX v Azure.  Vyberte **předplatné** a **skupinu prostředků** , do kterých chcete nasadit virtuální počítač a přidružené prostředky.

   > [!NOTE]
   > Při nasazování DCsv2-Series virtuálních počítačů v Azure jsou k dispozici nějaká omezení. Možná budete muset požádat o kvótu pro další jádra. Další informace najdete v tématu věnovaném [důvěrným výpočetním řešením na virtuálních počítačích Azure](./virtual-machine-solutions.md) .

1. Vyberte dostupnou oblast.
1. Do **názvu uzlu** zadejte název vašeho virtuálního počítače.
1. Zadejte uživatelské jméno a heslo (nebo klíč SSH) pro ověření do virtuálního počítače.
1. Ponechte výchozí velikost disku s operačním systémem jako 200 a vyberte velikost virtuálního počítače (Standard_DC4s_v2 pro tento kurz postačí).
1. Vložte token, který jste dříve vygenerovali v **tokenu JOIN**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="Snímek obrazovky, který ukazuje, jak nasadit prostředek.":::

1. Vyberte **Zkontrolovat a vytvořit**. Zajistěte, aby ověřování proběhlo úspěšně, a pak vyberte **vytvořit**. Po nasazení všech prostředků je výpočetní uzel teď zaregistrovaný ve Fortanix důvěrného Computing Manageru.

## <a name="run-the-application-image-on-the-compute-node"></a>Spuštění image aplikace na výpočetním uzlu

Spusťte aplikaci spuštěním následujícího příkazu. Ujistěte se, že jste změnili IP adresu uzlu, port a převedený název obrázku jako vstupy pro vaši konkrétní aplikaci.

V tomto kurzu je příkaz, který se má provést:

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

Kde:

- *52.152.206.164* je IP adresa hostitele agenta uzlu
- *9092* je port, který agent uzlu naslouchá.
- *fortanix-Private/Python-baněk-SGX* je převedená aplikace, kterou najdete na kartě image ve sloupci **název obrázku** v tabulce **imagí** na webovém portálu fortanix důvěrného Computing Manageru.

## <a name="verify-and-monitor-the-running-application"></a>Ověřit a monitorovat běžící aplikaci

1. Vraťte se do [Fortanix důvěrného Computing Manageru](https://ccm.fortanix.com/console).
1. Ujistěte se, že pracujete v **účtu** , kde jste uzel zaregistrovali.
1. V levém navigačním podokně vyberte ikonu nahoře a otevřete **konzolu pro správu** .
1. Vyberte kartu **aplikace** .
1. Ověřte, že je spuštěná aplikace s přidruženým výpočetním uzlem.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, virtuální počítač a přidružené prostředky. Odstraněním skupiny prostředků zrušíte registraci uzlů přidružených k převedené imagi.

Vyberte skupinu prostředků pro virtuální počítač a pak vyberte **Odstranit**. Potvrďte název skupiny prostředků, abyste dokončili odstraňování prostředků.

Pokud chcete odstranit účet Fortanix důvěrného Computing Manageru, který jste vytvořili, přečtěte si [stránku účty](https://ccm.fortanix.com/accounts) v Fortanix důvěrného Computing Manageru. Najeďte myší na účet, který chcete odstranit. Vyberte svislé černé tečky v pravém horním rohu a vyberte **Odstranit účet**.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="Snímek obrazovky, který ukazuje, jak účet odstranit":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili nástroje Fortanix k převedení image aplikace tak, aby běžely na důvěrném výpočetním virtuálním počítači. Další informace o důvěrných výpočetních virtuálních počítačích v Azure najdete v tématu [řešení na Virtual Machines](virtual-machine-solutions.md).

Další informace o nabídkách důvěrného výpočetního prostředí Azure najdete v tématu [Přehled důvěrných výpočtů Azure](overview.md).

Naučte se, jak provádět podobné úlohy pomocí dalších nabídek třetích stran v Azure, jako jsou [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) a [Scone](https://sconedocs.github.io).