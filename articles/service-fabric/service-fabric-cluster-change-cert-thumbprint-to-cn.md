---
title: Aktualizace clusteru tak, aby používal běžný název certifikátu
description: Přečtěte si, jak převést Service Fabric certifikát clusteru z deklarací založených na kryptografických otiskech na běžné názvy.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368056"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Převod certifikátů clusteru z deklarací založených na kryptografických otiskech na běžné názvy
Podpis certifikátu (colloquially označovaného jako kryptografický otisk) je jedinečný, což znamená, že certifikát clusteru deklarovaný kryptografickým otiskem odkazuje na konkrétní instanci certifikátu. To zase usnadňuje změnu a správu certifikátů, obecně obtížné a explicitní: každá změna vyžaduje orchestraci upgradů clusteru a základních hostitelských počítačů. Převádění deklarací certifikátů Service Fabric clusteru z kryptografických otisků, které jsou založeny na běžném názvu subjektu certifikátu, zjednodušuje správu, a to zejména v případě, že by certifikát už nevyžadoval upgrade clusteru. Tento článek popisuje, jak převést existující cluster na běžné deklarace založené na názvu bez výpadků.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>Přechod k certifikátům podepsaným certifikační autoritou (CA)
Zabezpečení clusteru, jehož certifikát je deklarován pomocí kryptografického otisku, uchovává skutečnost, že není možné, nebo výpočetní výkon pro zfalšovat certifikátu se stejnou signaturou jako jiný. V tomto případě je provenience certifikátu méně důležitá, takže certifikáty podepsané svým držitelem jsou dostatečné. Naproti tomu zabezpečení clusteru pomocí certifikátů deklarovaných pomocí běžných názvů toků ze služby infrastruktury veřejných klíčů (PKI), která tento certifikát vydala, a zahrnuje i aspekty, jako jsou postupy certifikace, bez ohledu na to, jestli je jejich provozní zabezpečení auditováné a mnoho dalších. Z tohoto důvodu je důležité zvolit infrastrukturu veřejných klíčů (PKI), Intimate znalosti vystavitelů (certifikační autorita nebo CA) a certifikáty podepsané svým držitelem jsou v podstatě bezcenné. Certifikát deklarovaný běžným názvem (CN) se obvykle považuje za platný, pokud se jeho ŘETĚZEK dá úspěšně sestavit, subjekt má očekávaný element CN a jeho Vystavitel (bezprostřední nebo vyšší v řetězu) je důvěryhodný agentem, který provádí ověřování. Service Fabric podporuje deklaraci certifikátů podle CN s vystavitelem implicitní (řetěz musí končit kotvou vztahu důvěryhodnosti), nebo s vystaviteli deklarovanými pomocí kryptografického otisku ("připnutí vydavatele"); Další podrobnosti najdete v tomto  [článku](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) . Chcete-li převést cluster pomocí certifikátu podepsaného svým držitelem deklarovaného kryptografickým otiskem na běžný název, musí být cílový certifikát podepsaný certifikační autoritou nejprve zaveden do clusteru podle kryptografického otisku. pouze pak je převod z TRANSAKČNÍho do CN možný.

Pro účely testování může být certifikát podepsaný svým držitelem deklarovaný pomocí CN, připnutí vystavitele na svůj vlastní kryptografický otisk; z hlediska zabezpečení je to skoro stejné jako deklarace stejného certifikátu podle TRANSAKČNÍho programu. Upozorňujeme však, že úspěšný převod tohoto druhu nezaručuje úspěšný převod z TRANSAKČNÍho na CN s certifikátem podepsaným certifikační autoritou. Proto se doporučuje testovat převod s řádným certifikátem podepsaným certifikační autoritou (existují bezplatné možnosti).

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Nahrajte certifikát a nainstalujte ho do sady škálování.
Doporučený mechanismus pro získání a zřizování certifikátů v Azure zahrnuje službu Azure Key Vault a její nástroje. Certifikát, který odpovídá deklaraci certifikátu clusteru, se musí zřídit do každého uzlu sady škálování virtuálních počítačů, které tvoří cluster. Další podrobnosti najdete [v tajných informacích o Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) . Před prováděním změn v deklaracích certifikátů clusteru je důležité, aby byly aktuální i cílové certifikáty clusteru nainstalovány na virtuálních počítačích každého typu uzlu clusteru. Cesta od vystavení certifikátu ke zřízení na uzel Service Fabric je podrobněji popsána [zde](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-cluster-to-an-optimal-starting-state"></a>Přepnout cluster do optimálního počátečního stavu
Převádění deklarace certifikátu z kryptografických otisků na základě běžného názvu ovlivňuje:

- Jak každý uzel v clusteru vyhledává a prezentuje své přihlašovací údaje k jiným uzlům
- Jak každý uzel ověřuje přihlašovací údaje svého protějšku při navázání zabezpečeného připojení  

Než budete pokračovat, zkontrolujte [pravidla prezentace a ověření pro obě konfigurace](cluster-security-certificates.md#certificate-configuration-rules) . Nejdůležitější pozornost při provádění převodu kryptografických otisků na běžný název je, že upgradované a dosud Neupgradované uzly (tj. uzly patřící do různých upgradovacích domén) musí umožňovat úspěšné vzájemné ověřování kdykoli během upgradu. Doporučený způsob, jak toho dosáhnout, je deklarovat cílový nebo cílový certifikát podle kryptografického otisku v počátečním upgradu a dokončit přechod na běžný název v pozdějším případě. Pokud je cluster již v doporučeném počátečním stavu, bude možné tento oddíl přeskočit.

Pro převod existuje více platných počátečních stavů. invariantní je, že cluster už používá cílový certifikát (deklarovaný pomocí kryptografického otisku) na začátku upgradu na běžný název. Zvažujeme `GoalCert` , `OldCert1` `OldCert2` :

#### <a name="valid-starting-states"></a>Platné počáteční stavy
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, kde `GoalCert` má pozdější `NotAfter` datum než `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, kde `GoalCert` má pozdější `NotAfter` datum než `OldCert1`

Pokud váš cluster není v jednom z výše uvedených platných stavů, přečtěte si článek o dosažení tohoto stavu na konci tohoto článku.

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>Vyberte požadované schéma ověřování certifikátů založeného na běžných názvech.
Jak bylo popsáno dříve, Service Fabric podporuje deklaraci certifikátů podle CN s implicitním kotvou vztahu důvěryhodnosti nebo explicitním připnutím kryptografických otisků vystavitele. Podrobnosti najdete v [tomto článku](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) , abyste měli jistotu, že máte dobré znalosti o rozdílech a důsledky výběru obou mechanismů. Syntakticky, tento rozdíl/volba je určena hodnotou `certificateIssuerThumbprintList` parametru: prázdné znamená, že se spoléhá na důvěryhodnou kořenovou certifikační autoritu (kotvu vztahu důvěryhodnosti), zatímco sada kryptografických otisků omezuje povolené přímé vystavitele certifikátů clusteru.

   > [!NOTE]
   > Pole certificateIssuerThumbprint umožňuje zadat očekávané přímé vystavitele certifikátů deklarovaných běžným názvem subjektu. Přijatelné hodnoty jsou jeden nebo více kryptografických otisků SHA1 oddělených čárkami. Všimněte si, že se jedná o posílení ověřování certifikátu – Pokud nejsou zadáni žádní vydavatelé nebo seznam je prázdný, certifikát bude přijat k ověřování, pokud je jeho řetěz sestavený a končí v kořenu, který validátor považuje za důvěryhodný. Je-li zadán jeden nebo více kryptografických otisků vystavitelů, bude certifikát přijat, pokud kryptografický otisk jeho přímého vystavitele, který je extrahován z řetězce, odpovídá libovolné hodnotě zadané v tomto poli – bez ohledu na to, zda je kořen důvěryhodný nebo nikoli. Infrastruktura veřejných klíčů (PKI) může používat jiné certifikační autority (vystavitele) k podepisování certifikátů s daným subjektem, takže je důležité pro tento předmět zadat všechny očekávané kryptografické otisky vystavitele. Jinými slovy, prodloužení platnosti certifikátu nesmí být podepsáno stejným vydavatelem jako certifikát, který se právě obnovuje.
   >
   > Zadání vystavitele je považováno za osvědčený postup; i když tento parametr vynecháte, bude i nadále fungovat – pro certifikáty, které se řetězí až k důvěryhodnému kořenovému adresáři – toto chování má omezení a v blízké budoucnosti může být ukončeno. Upozorňujeme také, že clustery nasazené v Azure a zabezpečené pomocí certifikátů x509 vydaných privátní infrastrukturou veřejných klíčů (pro komunikaci mezi clustery) nemůžou ověřit služba Azure Service Fabric (pro komunikaci mezi clustery), pokud zásada certifikátu PKI není zjistitelná, dostupná a přístupná. 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>Aktualizujte šablonu Azure Resource Management (ARM) clusteru a nasaďte ji.
Doporučuje se spravovat clustery Azure Service Fabric s využitím šablon ARM. alternativou je také použití artefaktů JSON [Azure Resource Explorer (Preview)](https://resources.azure.com). V tuto chvíli není v Azure Portal k dispozici ekvivalentní prostředí. Pokud není k dispozici původní šablona odpovídající existujícímu clusteru, můžete v Azure Portal získat ekvivalentní šablonu tak, že přejdete do skupiny prostředků obsahující cluster, vyberete **Exportovat šablonu** z nabídky na levé straně **Automatizace** a pak dále vyberete požadované prostředky. minimálně je potřeba exportovat sadu škálování virtuálního počítače a prostředky clusteru. Vygenerovanou šablonu lze také stáhnout. Poznámka: Tato šablona může vyžadovat změny před jejich úplným nasazením a nemusí přesně odpovídat původnímu stavu. Jedná se o reflexi aktuálního stavu prostředku clusteru.

Nezbytné změny jsou následující:
    - aktualizace definice rozšíření Service Fabric uzlu (v rámci prostředku virtuálního počítače); Pokud cluster definuje více typů uzlů, bude nutné aktualizovat definici každé odpovídající sady škálování virtuálních počítačů.
    - aktualizuje se definice prostředků clusteru.

Níže jsou uvedené podrobné příklady.

### <a name="updating-the-virtual-machine-scale-set-resources"></a>Aktualizují se prostředky sady škálování virtuálních počítačů.
Z
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
Záměr
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

### <a name="updating-the-cluster-resource"></a>Aktualizuje se prostředek clusteru.
Do prostředku **Microsoft. ServiceFabric/clustery** přidejte vlastnost **CertificateCommonNames** s nastavením **commonNames** a úplně odstraňte vlastnost **certifikátu** (všechna jeho nastavení):

Z
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
Záměr
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

Další informace najdete v tématu [nasazení clusteru Service Fabric, který místo kryptografického otisku používá běžný název certifikátu.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony
Po provedení změn znovu nasaďte aktualizovanou šablonu.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Příloha: dosažení platného počátečního stavu pro převod clusteru na deklarace certifikátů na základě CN

| Počáteční stav | Upgrade 1 | Upgrade 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` a `GoalCert` má pozdější `NotAfter` datum než `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` a `OldCert1` má pozdější `NotAfter` datum než `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, kde `OldCert1` má pozdější `NotAfter` datum než `GoalCert` | Upgradovat na `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, kde `OldCert1` má pozdější `NotAfter` datum než `GoalCert` | Upgradovat na `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Odebrat jeden z `OldCert1` nebo `OldCert2` pro získání stavu `Thumbprint: OldCertx, ThumbprintSecondary: None` | Pokračovat z nového počátečního stavu |

Pokyny, jak provést některý z těchto upgradů, najdete v [tomto dokumentu](service-fabric-cluster-security-update-certs-azure.md).


## <a name="next-steps"></a>Další kroky
* Přečtěte si o [zabezpečení clusteru](service-fabric-cluster-security.md).
* Informace o tom, jak [vyměnit certifikát clusteru podle společného názvu](service-fabric-cluster-rollover-cert-cn.md)
* Zjistěte, jak [nakonfigurovat cluster pro Bezdotykový efekt přechodu](cluster-security-certificate-management.md) .

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
