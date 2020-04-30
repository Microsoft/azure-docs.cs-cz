---
title: Vývoj pro týmovou práci pomocí .NET Core a sady Visual Studio
services: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: DrEsteban
ms.author: stevenry
ms.date: 12/09/2018
ms.topic: tutorial
description: V tomto kurzu se dozvíte, jak používat Azure Dev Spaces a Visual Studio k vývoji týmu v aplikaci .NET Core ve službě Azure Kubernetes
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s '
ms.openlocfilehash: c84c77fe7a425318700903427ff1c4aaa4e73a11
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82166032"
---
# <a name="team-development-using-net-core-and-visual-studio-with-azure-dev-spaces"></a>Vývoj pro týmovou práci pomocí .NET Core a sady Visual Studio s Azure Dev Spaces

V tomto kurzu se dozvíte, jak může tým vývojářů současně spolupracovat ve stejném clusteru Kubernetes pomocí vývojových prostorů.

## <a name="learn-about-team-development"></a>Informace o týmovém vývoji

Zatím jste kód aplikace spouštěli tak, jako byste byli jediným vývojářem, který na aplikaci pracuje. V této části se dozvíte, jak služba Azure Dev Spaces zjednodušuje týmový vývoj:
* Umožněte týmu vývojářů pracovat ve stejném prostředí, při práci ve sdíleném prostoru pro vývoj nebo v různých prostorech pro vývoj podle potřeby.
* Podporuje iterace kódu každého vývojáře v izolaci a bez obav z narušování práce ostatních.
* Umožňuje komplexní testování kódu před jeho potvrzením, bez nutnosti vytvářet napodobení nebo simulovat závislosti.

### <a name="challenges-with-developing-microservices"></a>Problémy při vývoji mikroslužeb
Vaše ukázková aplikace není v současnosti složitá. Když ale při vývoji v reálném světě budete přidávat další služby a vývojový tým se bude zvětšovat, začnou brzy vznikat problémy.

* Váš vývojový počítač nemusí mít dostatek prostředků ke spuštění každé služby, kterou potřebujete.
* Některé služby možná budou muset být veřejně dosažitelné. Služba může například potřebovat koncový bod, který reaguje na Webhook.
* Pokud chcete spustit podmnožinu služeb, musíte znát celou hierarchii závislostí mezi všemi vašimi službami. Určování této hierarchie může být obtížné, zejména v případě, že se zvyšuje počet služeb.
* Někteří vývojáři se uchylují k simulacím nebo napodobením řady závislostí svých služeb. Tento přístup může pomáhat, ale Správa těchto napodobenin může brzy ovlivnit náklady na vývoj. Tento přístup navíc vede k vašemu vývojovému prostředí, které se liší od výroby, což může vést k drobným chybám.
* Postup provádění jakéhokoli typu integračního testování se bude obtížně provádět. Testování integrace se dá věrohodně provést až po potvrzení, což znamená, že se problémy projeví později ve vývojovém cyklu.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Práce ve sdíleném vývojovém prostoru
Pomocí služby Azure Dev Spaces můžete nastavit *sdílený* vývojový prostor v Azure. Každý vývojář se může zaměřit jenom na svoji část aplikace a může iterativně vyvíjet *kód před potvrzením* ve vývojovém prostoru, který už obsahuje všechny ostatní služby a cloudové prostředky, na kterých jsou příslušné scénáře závislé. Závislosti jsou vždycky aktuální a vývojáři pracují způsobem, který odpovídá produkčnímu prostředí.

### <a name="work-in-your-own-space"></a>Práce ve vašem vlastním prostoru
Než je kód při vývoji pro vaši službu připravený k odevzdání, často není ve funkčním stavu. Pořád ho iterativně vytváříte, testujete ho a experimentujete s různými řešeními. Služba Azure Dev Spaces nabízí koncept **prostoru**, který umožňuje pracovat v izolaci a bez obav z narušení práce jiných členů vašeho týmu.

## <a name="use-dev-spaces-for-team-development"></a>Použití vývojových prostorů pro vývoj v týmu
Pojďme předvést tyto nápady pomocí konkrétního příkladu s naší ukázkovou aplikací *webendu* -> *mywebapi* . Představme si situaci, kdy vývojář, Scott, potřebuje provést změnu služby *mywebapi* a *jenom* tuto službu. Webfront- *Endu* se nebude muset změnit jako součást Scott Update.

_Bez_ použití vývojových prostorů by Scott měl několik způsobů vývoje a testování jeho aktualizace, přičemž žádná z nich není ideální:
* Spustit všechny součásti místně, což vyžaduje výkonnější vývojový počítač s nainstalovaným Docker a potenciálně MiniKube.
* Spustí všechny komponenty v izolovaném oboru názvů v clusteru Kubernetes. Vzhledem k tomu, že se *webendu* nemění, je použití izolovaného oboru názvů odpadem prostředků clusteru.
* Spouštějte jenom *mywebapi*a proveďte manuální volání REST. Tento typ testování netestuje celý tok na konci.
* Přidejte do *webendu* kód zaměřený na vývoj, který vývojářům umožňuje odesílat požadavky na jinou instanci *mywebapi*. Přidání tohoto kódu ztěžuje službu *webendu* .

### <a name="set-up-your-baseline"></a>Nastavení standardních hodnot
Nejprve bude nutné nasadit směrný plán našich služeb. Toto nasazení bude představovat "poslední známé funkční", takže můžete snadno porovnat chování místního kódu a verze vráceného se změnami. Pak vytvoříme podřízený prostor na základě tohoto směrného plánu, abychom mohli otestovat naše změny v *mywebapi* v kontextu větší aplikace.

1. Naklonujte [ukázkovou aplikaci pro vývojové prostory](https://github.com/Azure/dev-spaces):`git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Podívejte se na *azds_updates*vzdálené větve:`git checkout -b azds_updates origin/azds_updates`
1. Zavřete všechny ladicí relace (otevřené pomocí F5) pro obě služby, ale nechte projekty v oknech sady Visual Studio otevřené.
1. Přepněte do okna sady Visual Studio pomocí projektu _mywebapi_ .
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Vlastnosti**.
1. Výběrem karty **Ladění** na levé straně zobrazte nastavení Azure Dev Spaces.
1. Vyberte **změnit** , pokud chcete vytvořit prostor, který se použije při F5 nebo CTRL + F5 služby.
1. V rozevíracím seznamu prostor vyberte ** \<vytvořit nové místo... \>**.
1. Ujistěte se, že je nadřazený prostor nastavený ** \<na\>žádná**, a zadejte název místa pro **vývoj**. Klikněte na tlačítko OK.
1. Stisknutím kombinace kláves CTRL + F5 spustíte _mywebapi_ bez připojeného ladicího programu.
1. Přepněte do okna aplikace Visual Studio s projektem _webendu_ a stiskněte kombinaci kláves CTRL + F5 a spusťte ho také.

> [!Note]
> Někdy je při stisknutí Ctrl+F5 nutné po prvním zobrazení webové stránky v prohlížeči danou stránku aktualizovat.

> [!TIP]
> Výše uvedené kroky ručně nastaví směrný plán, ale doporučujeme, aby týmy používaly CI/CD k automatickému udržování směrného plánu v aktuálním stavu pomocí potvrzeného kódu.
>
> Pokud chcete vytvořit pracovní postup podobný následujícímu diagramu, přečtěte si náš průvodce vytvořením [CI/CD pomocí Azure DevOps](how-to/setup-cicd.md) .
>
> ![Příklad diagramu CI/CD](media/common/ci-cd-complex.png)

Kdokoli, kdo otevře veřejnou adresu URL a přejde k webové aplikaci, vyvolá cestu k kódu, kterou jste napsali, která se spouští prostřednictvím obou služeb pomocí výchozího prostoru pro _vývoj_ . Nyní předpokládejme, že chcete pokračovat ve vývoji *mywebapi* – jak to můžete udělat a nepřerušit jiné vývojáře, kteří používají místo pro vývoj? Vytvoříte si k tomu vlastní prostor.

### <a name="create-a-new-dev-space"></a>Vytvoření nového vývojového prostoru
V sadě Visual Studio můžete vytvářet další prostory, které se použijí, když pro danou službu stisknete F5 nebo Ctrl+F5. Prostor můžete pojmenovat libovolně (např. _sprint4_ nebo _demo_).

K vytvoření nového prostoru použijte následující postup:
1. Přepněte do okna sady Visual Studio pomocí projektu *mywebapi* .
2. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Vlastnosti**.
3. Výběrem karty **Ladění** na levé straně zobrazte nastavení Azure Dev Spaces.
4. Odtud můžete změnit nebo vytvořit cluster nebo prostor, který se použije po stisknutí F5 nebo Ctrl+F5. *Zkontrolujte, že je vybraný vývojový prostor Azure, který jste vytvořili dříve*.
5. V rozevíracím seznamu prostor vyberte ** \<vytvořit nové místo... \>**.

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. V dialogovém okně **Přidat prostor** nastavte nadřazený prostor na **dev**a zadejte název nového prostoru. Můžete pro nový prostor použít svoje jméno (například „scott“), aby bylo vašim kolegům jasné, že se jedná o prostor, ve kterém pracujete vy. Klikněte na tlačítko **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Teď by se měl zobrazit váš cluster AKS a na stránce vlastností projektu by měl být vybraný nový prostor.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Aktualizace kódu pro *mywebapi*

1. V projektu *mywebapi* proveďte změnu kódu `string Get(int id)` metody v souboru `Controllers/ValuesController.cs` následujícím způsobem:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Nastavte zarážku v tomto aktualizovaném bloku kódu (jednu už můžete mít nastavenou z dřívějška).
3. Stiskněte klávesu F5 a spusťte službu _mywebapi_ , která spustí službu ve vašem clusteru s použitím vybraného místa. Vybrané místo v tomto případě je _Scott_.

Následující diagram vám pomůže porozumět tomu, jak různé prostory fungují. Fialová cesta zobrazuje požadavek přes _vývojové_ místo, což je výchozí cesta, která se používá, pokud k adrese URL není vloženo žádné místo. Růžová cesta zobrazuje požadavek prostřednictvím místa pro _vývoj/Scott_ .

![](media/common/Space-Routing.png)

Tato integrovaná funkce služby Azure Dev Spaces umožňuje komplexní testování kódu ve sdíleném prostředí bez toho, aby si každý vývojář musel znovu vytvářet všechny služby ve svém prostoru. Tyto cesty vyžadují přesměrování šíření hlaviček v kódu aplikace, jak ukazuje předchozí krok tohoto průvodce.

### <a name="test-code-running-in-the-_devscott_-space"></a>Testování kódu spuštěného v prostoru pro _vývoj/Scott_
Pokud chcete otestovat novou verzi *mywebapi* ve spojení s *webendu*, otevřete svůj prohlížeč na adrese URL veřejného přístupového bodu pro *webendu* (například `http://dev.webfrontend.123456abcdef.eus.azds.io`) a přejděte na stránku o produktu. Měla by se zobrazit původní zpráva: „Hello from webfrontend and Hello from mywebapi.“

Teď k adrese URL přidejte „scott.s.“. do adresy URL tak, aby vypadala jako http\://Scott.s.dev.webfrontend.123456abcdef.EUS.azds.IO a aktualizovala prohlížeč. Zarážka, kterou jste nastavili v projektu *mywebapi* , by měla mít přístup. Pokračujte kliknutím na F5. V prohlížeči by se teď měla zobrazit nová zpráva „Hello from webfrontend and mywebapi now says something new.“ Důvodem je to, že cesta k aktualizovanému kódu v *mywebapi* je spuštěná v prostoru pro _vývoj/Scott_ .

Jakmile budete mít místo pro _vývoj_ , které vždycky obsahuje vaše nejnovější změny, a za předpokladu, že vaše aplikace je navržená tak, aby využila výhod směrování založeného na DevSpace, jak je popsáno v tomto kurzu, snad se snadno podívat, jak se můžou vývojové prostory významně pomáhat při testování nových funkcí v kontextu větší aplikace. Místo toho, abyste museli nasazovat _všechny_ služby do privátního prostoru, můžete vytvořit soukromý prostor, který je odvozený od _vývoje_, a jenom služby, na kterých skutečně pracujete. Infrastruktura směrování pro vývoj pro vývojová prostředí zpracuje zbytek tím, že se z vašeho privátního prostoru dokončí tolik služeb, jak může najít, zatímco se ve výchozím nastavení vrátí na nejnovější verzi, která běží ve _vývojovém_ prostoru. A stále ještě _víc_ vývojářům může aktivně vyvíjet různé služby ve stejnou dobu, aniž by navzájem narušily vzájemné přerušení.

### <a name="well-done"></a>Hotovo!
Dokončili jste úvodní příručku! Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nastavit Azure Dev Spaces se spravovaným clusterem Kubernetes v Azure
> * Iterativně vyvíjet kód v kontejnerech
> * Vyvíjet nezávisle dvě samostatné služby a pomocí zjišťování služby DNS v Kubernetes volat jinou službu
> * Produktivně vyvíjet a testovat kód v týmovém prostředí
> * Vytvořte základní hodnoty funkcí pomocí vývojových prostorů, abyste mohli snadno testovat izolované změny v kontextu větší aplikace mikroslužeb.

Teď když jste prozkoumali Azure Dev Spaces, [podělte se o svůj vývojářský prostor se členy týmu](how-to/share-dev-spaces.md) a ukažte jim, jak snadná je vzájemná spolupráce.

## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
azds controller list
```

```azurecli
az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
