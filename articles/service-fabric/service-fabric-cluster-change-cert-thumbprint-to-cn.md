---
title: Aktualizace clusteru tak, aby používal běžný název certifikátu
description: Přečtěte si, jak převést certifikát clusteru Azure Service Fabric z deklarací založených na kryptografických otiskech na běžné názvy.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: daf8d55e156f30b1f9e9ec5c50d60714e6f17884
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308009"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Převod certifikátů clusteru z deklarací založených na kryptografických otiskech na běžné názvy

Podpis certifikátu (obvykle se označuje jako kryptografický otisk) je jedinečný. Certifikát clusteru deklarovaný pomocí kryptografického otisku odkazuje na konkrétní instanci certifikátu. Tato specifičnost usnadňuje výměnu a správu certifikátů všeobecně, těžko a Explicit. Každá změna vyžaduje orchestraci upgradů clusteru a základních hostitelských počítačů.

Převádění deklarací certifikátů clusteru Azure Service Fabric z kryptografických otisků na základě běžného názvu subjektu certifikátu usnadňuje správu významně. Konkrétně převrácení certifikátu už nevyžaduje upgrade clusteru. Tento článek popisuje, jak převést existující cluster na deklarace založené na CN bez výpadků.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Přesunout do certifikátů podepsaných certifikační autoritou

Zabezpečení clusteru, jehož certifikát je deklarovaný pomocí kryptografického otisku, se na skutečnost, že je nemožné nebo je neproveditelné, aby zfalšovat certifikát se stejnou signaturou jako jiný. V tomto případě je provenience certifikátu méně důležitá, takže certifikáty podepsané svým držitelem jsou dostatečné.

Naopak zabezpečení clusteru, jehož certifikáty jsou deklarovány toky CN z implicitního vztahu důvěryhodnosti, vlastník clusteru má svého poskytovatele certifikátů. Poskytovatel je služba infrastruktura veřejných klíčů (PKI), která certifikát vystavila. Vztah důvěryhodnosti je založený mimo jiné faktory na certifikačních postupech infrastruktury veřejných klíčů, ať už jsou jejich provozní zabezpečení auditovány a schváleny ještě jinými důvěryhodnými stranami atd.

Vlastník clusteru musí také mít podrobné znalosti o tom, které certifikační autority (CA) vydávají své certifikáty, protože se jedná o zásadní aspekt ověřování certifikátů podle předmětu. To také znamená, že certifikáty podepsané svým držitelem jsou pro tento účel zcela nevhodné. Doslova kdokoli může vygenerovat certifikát s daným subjektem.

Certifikát deklarovaný pomocí CN je obvykle považován za platný, pokud:

* Řetěz se dá úspěšně sestavit.
* Subjekt má očekávaný element CN.
* Jeho Vystavitel (bezprostřední nebo vyšší v řetězu) je důvěryhodný agentem, který provádí ověřování.

Service Fabric podporuje deklaraci certifikátů pomocí CN dvěma způsoby:

* U *implicitních* vystavitelů to znamená, že řetěz musí končit kotvou vztahu důvěryhodnosti.
* U vystavitelů deklarovaných pomocí kryptografického otisku, který se označuje jako připnutí vystavitele.

Další informace najdete v tématu [deklarace ověřování certifikátů založeného na běžných názvech](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Chcete-li převést cluster pomocí certifikátu podepsaného svým držitelem deklarovaného kryptografickým otiskem do CN, musí být cílový certifikát podepsaný certifikační autoritou nejprve zaveden do clusteru podle kryptografického otisku. Pouze pak je převod z kryptografického otisku na CN možný.

Pro účely testování *může* být certifikát podepsaný svým držitelem DEKLAROVÁN pomocí CN, ale pouze v případě, že je Vystavitel připnuté ke svému vlastnímu kryptografickému otisku. Z hlediska zabezpečení je tato akce skoro ekvivalentem deklarace stejného certifikátu pomocí kryptografického otisku. Úspěšný převod tohoto druhu nezaručuje úspěšný převod z kryptografického otisku na CN s certifikátem podepsaným certifikační autoritou. Doporučujeme otestovat převod s řádným certifikátem podepsaným certifikační autoritou. Pro toto testování existují bezplatné možnosti.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Nahrajte certifikát a nainstalujte ho do sady škálování.

Doporučený mechanismus pro získání a zřizování certifikátů v Azure zahrnuje Azure Key Vault a jeho nástroje. Certifikát odpovídající deklaraci certifikátu clusteru musí být zřízený do každého uzlu sady škálování virtuálních počítačů, které tvoří cluster. Další informace najdete v tématu [tajné klíče ve virtuálních počítačích Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#how-do-i-securely-ship-a-certificate-to-the-vm-).

Před provedením změn v deklaracích certifikátů clusteru je důležité nainstalovat aktuální i cílové certifikáty clusteru na virtuální počítače každého typu uzlu clusteru. Cesta od vystavení certifikátu až po zřízení do Service Fabricho uzlu je podrobněji popsána v [cestě k certifikátu](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>Převést cluster do optimálního počátečního stavu

Převod deklarace certifikátu z kryptografického otisku na základě dopadů na bázi CN:

- Jak každý uzel v clusteru vyhledává a prezentuje své přihlašovací údaje jiným uzlům.
- Jak každý uzel ověřuje přihlašovací údaje svého protějšku při navázání zabezpečeného připojení.

Než budete pokračovat, zkontrolujte [pravidla prezentace a ověření pro obě konfigurace](cluster-security-certificates.md#certificate-configuration-rules) . Nejdůležitějším aspektem při převodu kryptografických otisků na CN je to, že upgradované a dosud Neupgradované uzly (tj. uzly patřící do různých upgradovacích domén) musí být schopné provést kdykoli úspěšné vzájemné ověřování. Doporučený způsob, jak dosáhnout tohoto chování, je deklarovat cílový nebo cílový certifikát podle kryptografického otisku v počátečním upgradu. Pak dokončete přechod do CN v pozdějším případě. Pokud je cluster již v doporučeném počátečním stavu, můžete tuto část přeskočit.

Pro převod existuje několik platných počátečních stavů. Invariantní je, že cluster už používá cílový certifikát (deklarovaný pomocí kryptografického otisku) na začátku upgradu na CN. Zvažujeme `GoalCert` , `OldCert1` a `OldCert2` v tomto článku.

#### <a name="valid-starting-states"></a>Platné počáteční stavy

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, kde `GoalCert` má pozdější `NotBefore` datum než `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, kde `GoalCert` má pozdější `NotBefore` datum než `OldCert1`

> [!NOTE]
> Před verzí 7.2.445 (7,2 CU4) Service Fabric vybrali certifikát s nejstarším vypršením platnosti (certifikát s nejvyšším "NotAfter"), takže výše uvedené počáteční stavy před 7,2 CU4 vyžadují, aby GoalCert pozdější `NotAfter` datum než `OldCert1`

Pokud váš cluster není v některém z výše uvedených platných stavů, přečtěte si informace o tom, jak tento stav dosáhnout, v části na konci tohoto článku.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>Vyberte požadované schéma ověřování certifikátů založeného na CN.

Jak bylo popsáno dříve, Service Fabric podporuje deklaraci certifikátů pomocí CN s implicitním kotvou vztahu důvěryhodnosti nebo explicitním připnutím kryptografických otisků vystavitele. Další informace najdete v tématu [deklarace ověřování certifikátů založeného na běžných názvech](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Ujistěte se, že máte dobré znalosti o rozdílech a důsledky výběru obou mechanismů. Syntakticky, tento rozdíl nebo volba je určena hodnotou `certificateIssuerThumbprintList` parametru. Prázdné znamená, že se spoléhá na důvěryhodnou kořenovou certifikační autoritu (kotvu vztahu důvěryhodnosti), zatímco sada kryptografických otisků omezuje povolené přímé vystavitele certifikátů clusteru.

   > [!NOTE]
   > `certificateIssuerThumbprint`Pole vám umožní zadat očekávané přímé vystavitele certifikátů deklarovaných podle CN předmětu. Přijatelné hodnoty jsou jeden nebo více kryptografických otisků SHA1 oddělených čárkami. Tato akce posílí ověřování certifikátu.
   >
   > Pokud nejsou zadáni žádní vydavatelé nebo je seznam prázdný, certifikát bude přijat k ověřování, pokud lze jeho řetěz vytvořit. Certifikát pak skončí v kořenu, který validátor považuje za důvěryhodný. Pokud je zadán jeden nebo více kryptografických otisků vystavitelů, bude certifikát přijat, pokud otisk jeho přímého vystavitele, který je extrahován z řetězce, odpovídá libovolné hodnotě uvedené v tomto poli. Certifikát se přijme, pokud je kořenový adresář důvěryhodný.
   >
   > Infrastruktura veřejných klíčů (PKI) může použít jiné certifikační autority (označované také jako *Vystavitelé*) k podepisování certifikátů s daným subjektem. Z tohoto důvodu je důležité pro tento předmět zadat všechny očekávané kryptografické otisky vystavitele. Jinými slovy, prodloužení platnosti certifikátu nemůže být podepsáno stejným vydavatelem jako certifikát, který se právě obnovuje.
   >
   > Zadání vystavitele je považováno za osvědčený postup. Vynechání vystavitele bude i nadále fungovat pro certifikáty zřetězené až k důvěryhodnému kořenovému adresáři, ale toto chování má omezení a v blízké budoucnosti může být ukončeno. Clustery nasazené v Azure zabezpečené pomocí certifikátů x509 vydaných privátní infrastrukturou veřejných klíčů (PKI), které jsou deklarované subjektem, nemusí být možné ověřit pomocí Service Fabric (pro komunikaci mezi clustery). Ověření vyžaduje, aby byly zásady certifikátů infrastruktury veřejných klíčů dostupné, dostupné a dostupné.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>Aktualizace šablony Azure Resource Manager a nasazení clusteru

Spravujte Service Fabric clusterů pomocí šablon Azure Resource Manager (ARM). Alternativou, která také používá artefakty JSON, je [Azure Resource Explorer (Preview)](https://resources.azure.com). V tuto chvíli není v Azure Portal k dispozici ekvivalentní prostředí.

Pokud není k dispozici původní šablona odpovídající existujícímu clusteru, můžete v Azure Portal získat ekvivalentní šablonu. Přejděte do skupiny prostředků, která obsahuje cluster, a v nabídce **Automatizace** na levé straně vyberte **Exportovat šablonu** . Pak vyberte prostředky, které chcete. Minimálně je potřeba exportovat sadu škálování virtuálního počítače a prostředky clusteru. Vygenerovanou šablonu lze také stáhnout. Tato šablona může vyžadovat změny před jejich úplným nasazením. Šablona také nemusí přesně odpovídat původnímu. Jedná se o reflexi aktuálního stavu prostředku clusteru.

Nezbytné změny jsou následující:

- Aktualizuje se definice rozšíření Service Fabric uzlu (v prostředku virtuálního počítače). Pokud cluster definuje více typů uzlů, bude nutné aktualizovat definici každé odpovídající sady škálování virtuálních počítačů.
- Aktualizuje se definice prostředků clusteru.

Zde jsou uvedené podrobné příklady.

### <a name="update-the-virtual-machine-scale-set-resources"></a>Aktualizace prostředků sady škálování virtuálních počítačů
Z:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Do:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>Aktualizace prostředku clusteru

Do prostředku **Microsoft. ServiceFabric/clustery** přidejte vlastnost **CertificateCommonNames** s nastavením **commonNames** a úplně odstraňte vlastnost **certifikátu** (všechna jeho nastavení).

Z:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Do:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Další informace najdete v tématu [nasazení clusteru Service Fabric, který místo kryptografického otisku používá běžný název certifikátu](./service-fabric-create-cluster-using-cert-cn.md).

## <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony

Po provedení změn znovu nasaďte aktualizovanou šablonu.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Dosažení platného počátečního stavu pro převod clusteru na deklarace certifikátů na základě CN

| Počáteční stav | Upgrade 1 | Upgrade 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` a `GoalCert` má pozdější `NotBefore` datum než `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` a `OldCert1` má pozdější `NotBefore` datum než `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, kde `OldCert1` má pozdější `NotBefore` datum než `GoalCert` | Upgradovat na `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, kde `OldCert1` má pozdější `NotBefore` datum než `GoalCert` | Upgradovat na `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Odebrat jeden z `OldCert1` nebo `OldCert2` pro získání stavu `Thumbprint: OldCertx, ThumbprintSecondary: None` | Pokračovat z nového počátečního stavu |

> [!NOTE]
> Pro cluster na verzi starší než verze 7.2.445 (7,2 CU4) nahraďte `NotBefore` `NotAfter` ve výše uvedených stavech.

Pokyny, jak provést některý z těchto upgradů, najdete v tématu [Správa certifikátů v clusteru Azure Service Fabric](service-fabric-cluster-security-update-certs-azure.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [zabezpečení clusteru](service-fabric-cluster-security.md).
* Naučte se [převádět certifikát clusteru běžným názvem](service-fabric-cluster-rollover-cert-cn.md).
* Naučte se [konfigurovat cluster pro bezdotykovou výměnu](cluster-security-certificate-management.md).

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
