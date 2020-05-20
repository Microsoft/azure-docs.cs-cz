---
title: Podpora Azure IoT Hub pro virtuální sítě
description: Použití vzoru připojení virtuálních sítí s IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: jlian
ms.openlocfilehash: 61d24ac9f99a7c7b2b4d9ca6f3fd7b0a338341b8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652360"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Podpora IoT Hub pro virtuální sítě

V tomto článku se seznámíte se vzorem připojení virtuální sítě a naučíte se, jak nastavit privátní prostředí pro připojení ke službě IoT Hub prostřednictvím virtuální sítě Azure vlastněné zákazníkem.

> [!NOTE]
> Funkce IoT Hub popsané v tomto článku jsou aktuálně dostupné pro centra IoT [vytvořená pomocí identity spravované služby](#create-an-iot-hub-with-managed-service-identity) v následujících oblastech: Východní USA, Střed USA – jih a západní USA 2.


## <a name="introduction"></a>Úvod

Ve výchozím nastavení se IoT Hub názvy hostitelů mapují k veřejnému koncovému bodu s veřejně směrovatelné IP adresou přes Internet. Jak je znázorněno na obrázku níže, je tento IoT Hub veřejný koncový bod sdílen mezi rozbočovači, které vlastní různí zákazníci a k němu můžou používat zařízení IoT přes sítě v rozlehlých sítích i místní sítě.

Několik funkcí IoT Hub, včetně [směrování zpráv](./iot-hub-devguide-messages-d2c.md), [nahrávání souborů](./iot-hub-devguide-file-upload.md)a [hromadného importu/exportu zařízení](./iot-hub-bulk-identity-mgmt.md) , podobně vyžaduje připojení od IoT Hub k prostředku Azure vlastněné zákazníkem prostřednictvím jeho veřejného koncového bodu. Jak je znázorněno níže, tyto cesty připojení společně tvoří výstupní přenos dat z IoT Hub k zákaznickým prostředkům.
![IoT Hub veřejný koncový bod](./media/virtual-network-support/public-endpoint.png)


Z několika důvodů můžou zákazníci chtít omezit připojení ke svým prostředkům Azure (včetně IoT Hub) prostřednictvím virtuální sítě, kterou vlastní a funguje. Mezi tyto důvody patří:

* Zavedení dalších vrstev zabezpečení přes izolaci na úrovni sítě pro službu IoT Hub tím, že zabráníte expozici připojení k vašemu centru přes veřejný Internet.

* Díky možnosti privátního připojení z vašich místních síťových prostředků zajistíte přenos dat a přenosů přímo do páteřní sítě Azure.

* Prevence útoků exfiltrace z citlivých místních sítí. 

* Pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md)navázali vzory připojení na úrovni Azure.


Tento článek popisuje, jak dosáhnout těchto cílů pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md) pro připojení příchozího připojení k IoT Hub, jako je použití výjimky služby Azure Trusted First stran pro odchozí připojení z IoT Hub na jiné prostředky Azure.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Připojení příchozího připojení k IoT Hub pomocí privátních koncových bodů

Privátní koncový bod je privátní IP adresa přidělená v rámci virtuální sítě vlastněné zákazníkem, přes kterou je prostředek Azure dostupný. Díky tomu, že máte privátní koncový bod pro službu IoT Hub, budete moci umožnit, aby služby, které jsou ve vaší virtuální síti v provozu, mohly být dostupné IoT Hub bez nutnosti odesílat data do veřejného koncového bodu IoT Hub. Podobně zařízení, která pracují v místním prostředí, můžou používat [virtuální privátní síť (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) nebo soukromý partnerský vztah [ExpressRoute](https://azure.microsoft.com/services/expressroute/) k získání připojení k vaší virtuální síti v Azure a následně k vašemu IoT Hub (prostřednictvím svého privátního koncového bodu). Výsledkem je, že zákazníci, kteří chtějí omezit připojení ke svým veřejným koncovým bodům služby IoT Hub (nebo ho může zcela blokovat), můžou tento cíl dosáhnout pomocí [IoT Hub filtru IP](./iot-hub-ip-filtering.md) a [konfigurací směrování, aby neodesílala žádná data do integrovaného koncového bodu](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint). Tento přístup zachovává připojení ke svému centru pomocí privátního koncového bodu pro zařízení. Hlavním soustředěním tohoto nastavení je zařízení v místní síti. Tato instalace se nedoporučuje u zařízení nasazených v síti WAN.

![IoT Hub veřejný koncový bod](./media/virtual-network-support/virtual-network-ingress.png)

Než budete pokračovat, ujistěte se, že jsou splněné následující předpoklady:

* Vaše centrum IoT musí být zřízené s [identitou spravované služby](#create-an-iot-hub-with-managed-service-identity).

* Vaše centrum IoT se musí zřídit v některé z [podporovaných oblastí](#regional-availability-private-endpoints).

* Zřídili jste virtuální síť Azure s podsítí, ve které se vytvoří privátní koncový bod. Další podrobnosti najdete v tématu [vytvoření virtuální sítě pomocí Azure CLI](../virtual-network/quick-create-cli.md) .

* Pro zařízení, která pracují v místních sítích, nastavte [virtuální privátní síť (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) nebo privátní partnerský vztah [ExpressRoute](https://azure.microsoft.com/services/expressroute/) do virtuální sítě Azure.


### <a name="regional-availability-private-endpoints"></a>Regionální dostupnost (soukromé koncové body)

Soukromé koncové body podporované v IoT Hub vytvořeny v následujících oblastech:

* USA – východ

* USA – středojih

* USA – západ 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Nastavení privátního koncového bodu pro IoT Hub příchozí přenosy

K nastavení privátního koncového bodu použijte následující postup:

1. Spuštěním následujícího příkazu rozhraní příkazového řádku Azure znovu zaregistrujete poskytovatele služby Azure IoT Hub u svého předplatného:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Přejděte na kartu **připojení privátního koncového bodu** na portálu IoT Hub (Tato karta je k dispozici pouze pro centra IoT v [podporovaných oblastech](#regional-availability-private-endpoints)) a kliknutím na **+** znaménko přidejte nový soukromý koncový bod.

3. Zadejte předplatné, skupinu prostředků, název a oblast pro vytvoření nového privátního koncového bodu (v ideálním případě by se měl privátní koncový bod vytvořit ve stejné oblasti jako vaše centrum). Další informace najdete v [části věnované regionálním dostupnostem](#regional-availability-private-endpoints) .

4. Klikněte na **Další: prostředek**, zadejte předplatné pro svůj prostředek IoT Hub a jako typ prostředku vyberte **Microsoft. Devices/IotHubs** , jako **prostředek**IoT Hub název a jako cílový dílčí prostředek použijte **iotHub** .

5. Klikněte na **Další: Konfigurace** a zadejte virtuální síť a podsíť pro vytvoření privátního koncového bodu v nástroji. V případě potřeby vyberte možnost integrace s privátní zónou DNS Azure.

6. Klikněte na **Další: značky**a volitelně poskytněte pro svůj prostředek všechny značky.

7. Kliknutím na tlačítko **zkontrolovat + vytvořit** vytvořte prostředek privátního propojení.

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>Vestavěný koncový bod kompatibilní s centrem událostí nepodporuje přístup prostřednictvím privátního koncového bodu.

[Předdefinovaný koncový bod kompatibilní](iot-hub-devguide-messages-read-builtin.md) s centrem událostí nepodporuje přístup prostřednictvím privátního koncového bodu. Po nakonfigurování je privátní koncový bod služby IoT Hub určen jenom pro připojení příchozího přenosu dat. Zpracování dat z integrovaného koncového bodu kompatibilního s centrem událostí se dá provést jenom přes veřejný Internet. 

[Filtr IP](iot-hub-ip-filtering.md) IoT Hub také neřídí veřejný přístup k předdefinovanému koncovému bodu. Pokud chcete zcela zablokovat přístup k veřejné síti ke službě IoT Hub, musíte: 

1. Konfigurace přístupu k privátnímu koncovému bodu pro IoT Hub
1. Vypnout přístup k veřejné síti pomocí filtru IP pro blokování všech IP adres
1. Vypnutí vestavěného koncového bodu centra událostí [nastavením směrování na Neodesílat data](iot-hub-devguide-messages-d2c.md)
1. Vypnutí [záložní trasy](iot-hub-devguide-messages-d2c.md#fallback-route)
1. Konfigurace odchozích přenosů na jiné prostředky Azure pomocí [důvěryhodných služeb Azure First stran](#egress-connectivity-from-iot-hub-to-other-azure-resources)

### <a name="pricing-private-endpoints"></a>Ceny (soukromé koncové body)

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Odchozí připojení z IoT Hub k jiným prostředkům Azure

IoT Hub potřebuje přístup k úložišti objektů blob Azure, centrům událostí, prostředkům služby Service Bus pro [směrování zpráv](./iot-hub-devguide-messages-d2c.md), [nahrávání souborů](./iot-hub-devguide-file-upload.md)a [hromadnému importu/exportu zařízení](./iot-hub-bulk-identity-mgmt.md), což obvykle probíhá přes Veřejný koncový bod prostředků. V případě, že navážete účet úložiště, centra událostí nebo prostředek služby Service Bus do virtuální sítě, povedená konfigurace bude ve výchozím nastavení blokovat připojení k prostředku. V důsledku toho bude bránit IoT Hub funkce, které vyžadují přístup k těmto prostředkům.

Abyste tuto situaci zmírnili, musíte povolit připojení z prostředku IoT Hub k vašemu účtu úložiště, centra událostí nebo prostředkům služby Service Bus prostřednictvím možnosti **důvěryhodné služby Azure First stran** .

Požadavky jsou následující:

* Vaše centrum IoT se musí zřídit v některé z [podporovaných oblastí](#regional-availability-trusted-microsoft-first-party-services).

* Vašemu IoT Hub musí být přiřazena identita spravované služby v době zřizování centra. Postupujte podle pokynů k [vytvoření centra s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Regionální dostupnost (důvěryhodné služby Microsoft First stran)

Výjimka služby Azure Trusted First stran pro obejít omezení brány firewall do úložiště Azure, centra událostí a prostředky služby Service Bus se podporují jenom pro centra IoT v následujících oblastech:

* USA – východ

* USA – středojih

* USA – západ 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Ceny (důvěryhodné služby Microsoft First stran)

Důvěryhodná funkce výjimky služby Microsoft First stran je v [podporovaných oblastech](#regional-availability-trusted-microsoft-first-party-services)zdarma v centrech pro IoT. Poplatky za zřízené účty úložiště, centra událostí nebo prostředky služby Service Bus se vztahují samostatně.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Vytvoření služby IoT Hub s identitou spravované služby

Identitu spravované služby je možné přiřadit ke svému centru během zřizování prostředků (Tato funkce se v současné době nepodporuje pro existující centra), což vyžaduje, aby služba IoT Hub používala TLS 1,2 jako minimální verzi. Pro tento účel musíte použít níže uvedenou šablonu prostředků ARM:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Po nahrazení hodnot prostředku `name` , `location` `SKU.name` a `SKU.tier` můžete pomocí Azure CLI nasadit prostředek do existující skupiny prostředků pomocí:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Po vytvoření prostředku můžete načíst identitu spravované služby přiřazenou k vašemu centru pomocí Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Po zřízení IoT Hub s identitou spravované služby použijte odpovídající část k nastavení koncových bodů směrování na [účty úložiště](#egress-connectivity-to-storage-account-endpoints-for-routing), [centra událostí](#egress-connectivity-to-event-hubs-endpoints-for-routing)a prostředky [služby Service Bus](#egress-connectivity-to-service-bus-endpoints-for-routing) nebo nakonfigurujte [nahrávání souborů](#egress-connectivity-to-storage-accounts-for-file-upload) a [hromadné importy a exporty zařízení](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport).


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Odchozí připojení k koncovým bodům účtu úložiště pro směrování

IoT Hub můžete nakonfigurovat tak, aby směroval zprávy na účet úložiště ve vlastnictví zákazníka. Aby funkce směrování umožnila přístup k účtu úložiště, když jsou zavedena omezení brány firewall, musí mít vaše IoT Hub identitu spravované služby (viz jak [vytvořit centrum s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity)). Po zřízení identity spravované služby postupujte podle následujících kroků a Udělte identitě prostředků vašeho rozbočovače oprávnění RBAC pro přístup k vašemu účtu úložiště.

1. V Azure Portal přejděte na kartu **řízení přístupu (IAM)** účtu úložiště a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte **Přispěvatel dat objektu BLOB úložiště** jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a vyberte název prostředku IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. V účtu úložiště přejděte na kartu **brány firewall a virtuální sítě** a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko pro **Povolení přístupu k tomuto účtu úložiště důvěryhodným službám Microsoftu**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředku IoT Hub přejděte na kartu **směrování zpráv** .

5. Přejděte do části **vlastní koncové body** a klikněte na **Přidat**. Jako typ koncového bodu vyberte **úložiště** .

6. Na stránce, která se zobrazí, zadejte název koncového bodu, vyberte kontejner, který chcete použít ve službě BLOB Storage, zadejte kódování a formát názvu souboru. Jako **typ ověřování** vyberte **systém přiřazený** ke koncovému bodu úložiště. Klikněte na tlačítko **Vytvořit**.

Vlastní koncový bod úložiště je teď nastavený tak, aby používal identitu přiřazenou systémem vašeho rozbočovače a měl oprávnění pro přístup k vašemu prostředku úložiště Navzdory omezením brány firewall. Nyní můžete použít tento koncový bod k nastavení pravidla směrování.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Odchozí připojení k koncovým bodům centra událostí pro směrování

IoT Hub je možné nakonfigurovat tak, aby směroval zprávy do oboru názvů centra událostí vlastněných zákazníkem. Aby funkce směrování umožnila přístup k prostředku centra událostí, zatímco jsou zavedena omezení brány firewall, musí mít vaše IoT Hub identitu spravované služby (viz jak [vytvořit centrum s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity)). Po zřízení identity spravované služby použijte následující postup a udělte jí oprávnění RBAC pro přístup k vašemu centru událostí.

1. V Azure Portal přejděte na kartu IAM (Event hub **Access Control)** a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte **Event Hubs datový odesílatel** jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a vyberte název prostředku IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. Na kartě centra událostí přejděte na kartu **brány firewall a virtuální sítě** a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko, aby **důvěryhodné služby Microsoftu měly přístup k**centrům událostí. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředku IoT Hub přejděte na kartu **směrování zpráv** .

5. Přejděte do části **vlastní koncové body** a klikněte na **Přidat**. Jako typ koncového bodu vyberte možnost **centra událostí** .

6. Na stránce, která se zobrazí, zadejte název koncového bodu, vyberte obor názvů a instanci centra událostí a klikněte na tlačítko **vytvořit** .

Vaše vlastní koncový bod centra událostí je teď nastavený tak, aby používal identitu přiřazenou systémem vašeho centra, a má oprávnění k přístupu k vašemu prostředku centra událostí bez ohledu na jeho omezení brány firewall. Nyní můžete použít tento koncový bod k nastavení pravidla směrování.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Odchozí připojení ke koncovým bodům služby Service Bus pro směrování

IoT Hub je možné nakonfigurovat tak, aby směroval zprávy do oboru názvů služby Service Bus vlastněné zákazníkem. Aby funkce směrování umožnila přístup k prostředku služby Service Bus, když jsou zavedena omezení brány firewall, musí mít IoT Hub identitu spravované služby (viz jak [vytvořit centrum s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity)). Po zřízení identity spravované služby použijte následující postup a udělte jí oprávnění RBAC pro přístup k vaší službě Service Bus.

1. V Azure Portal přejděte na kartu **řízení přístupu (IAM)** služby Service Bus a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte možnost **odesílatel dat Service Bus** jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a v rozevíracím seznamu vyberte název prostředku IoT Hub. Klikněte na tlačítko **Uložit**.

3. Přejděte na kartu **brány firewall a virtuální sítě** ve službě Service Bus a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko pro **Povolení přístupu ke službě Service Bus důvěryhodným službám Microsoftu**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředku IoT Hub přejděte na kartu **směrování zpráv** .

5. Přejděte do části **vlastní koncové body** a klikněte na **Přidat**. Jako typ koncového bodu vyberte položku **fronta služby Service Bus** nebo **téma Service Bus** (podle potřeby).

6. Na stránce, která se zobrazí, zadejte název koncového bodu, vyberte obor názvů služby Service Bus a frontu nebo téma (podle potřeby). Klikněte na tlačítko **Vytvořit**.

Vlastní koncový bod služby Service Bus je teď nastavený tak, aby používal identitu přiřazenou systémem vašeho rozbočovače a měl oprávnění k přístupu k prostředku služby Service Bus Navzdory omezením brány firewall. Nyní můžete použít tento koncový bod k nastavení pravidla směrování.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Odchozí připojení k účtům úložiště pro nahrání souboru

Funkce nahrávání souborů IoT Hub umožňuje zařízením nahrávat soubory do účtu úložiště ve vlastnictví zákazníka. Aby mohl nahrávání souboru fungovat, musí mít obě zařízení i IoT Hub připojení k účtu úložiště. Pokud jsou na účtu úložiště zavedena omezení brány firewall, musí vaše zařízení získat připojení pomocí některého z podporovaných mechanismů účtu úložiště (včetně [privátních koncových bodů](../private-link/create-private-endpoint-storage-portal.md), [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md) nebo [přímé konfigurace brány firewall](../storage/common/storage-network-security.md)). Podobně platí, že pokud jsou na účtu úložiště zavedena omezení brány firewall, IoT Hub musí být nakonfigurovaná pro přístup k prostředku úložiště přes důvěryhodnou výjimku služby Microsoftu. Pro účely tohoto účelu musí mít vaše IoT Hub identitu spravované služby (viz jak [vytvořit centrum s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity)). Po zřízení identity spravované služby postupujte podle následujících kroků a Udělte identitě prostředků vašeho rozbočovače oprávnění RBAC pro přístup k vašemu účtu úložiště.

1. V Azure Portal přejděte na kartu **řízení přístupu (IAM)** účtu úložiště a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte **Přispěvatel dat objektu BLOB úložiště** jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a vyberte název prostředku IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. V účtu úložiště přejděte na kartu **brány firewall a virtuální sítě** a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko pro **Povolení přístupu k tomuto účtu úložiště důvěryhodným službám Microsoftu**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředku IoT Hub přejděte na kartu **nahrávání souborů** .

5. Na stránce, která se zobrazí, vyberte kontejner, který chcete použít ve službě BLOB Storage, nakonfigurujte **Nastavení oznamování souborů**, **hodnotu TTL SAS**, **výchozí hodnotu TTL** a **maximální počet doručení** podle potřeby. Jako **typ ověřování** vyberte **systém přiřazený** ke koncovému bodu úložiště. Klikněte na tlačítko **Vytvořit**.

Teď je koncový bod úložiště pro nahrání souborů nastavený tak, aby používal identitu přiřazenou systémem vašeho rozbočovače a měl oprávnění pro přístup k vašemu prostředku úložiště Navzdory omezením brány firewall.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Odchozí připojení k účtům úložiště pro import/export hromadného zařízení

IoT Hub podporuje funkce pro [Import/export](./iot-hub-bulk-identity-mgmt.md) informací o zařízeních do objektu BLOB úložiště poskytovaného zákazníkem hromadně. Aby funkce hromadného importu a exportu mohla fungovat, musí mít obě zařízení i IoT Hub připojení k účtu úložiště.

Tato funkce vyžaduje připojení z IoT Hub k účtu úložiště. Aby bylo možné získat přístup k prostředku služby Service Bus, pokud jsou k dispozici omezení brány firewall, musí mít vaše IoT Hub identitu spravované služby (viz jak [vytvořit centrum s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity)). Po zřízení identity spravované služby použijte následující postup a udělte jí oprávnění RBAC pro přístup k vaší službě Service Bus.

1. V Azure Portal přejděte na kartu **řízení přístupu (IAM)** účtu úložiště a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte **Přispěvatel dat objektu BLOB úložiště** jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a vyberte název prostředku IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. V účtu úložiště přejděte na kartu **brány firewall a virtuální sítě** a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko pro **Povolení přístupu k tomuto účtu úložiště důvěryhodným službám Microsoftu**. Klikněte na tlačítko **Uložit**.

Teď můžete použít Azure IoT REST API pro [vytváření úloh importu exportu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) , kde najdete informace o tom, jak používat funkce hromadného importu a exportu. Všimněte si, že budete muset zadat `storageAuthenticationType="identityBased"` v textu žádosti a použít `inputBlobContainerUri="https://..."` a `outputBlobContainerUri="https://..."` jako vstupní a výstupní adresu URL vašeho účtu úložiště.


Azure IoT Hub SDK podporuje tuto funkci také ve Správci registru klienta služby. Následující fragment kódu ukazuje, jak iniciovat úlohu importu nebo úlohu exportu v rámci používání sady C# SDK.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Použití této verze sady SDK Azure IoT v oblasti s podporou virtuální sítě pro jazyky C#, Java a Node. js:

1. Vytvořte proměnnou prostředí s názvem `EnableStorageIdentity` a nastavte její hodnotu na `1` .

2. Stažení sady SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node. js](https://aka.ms/vnetnodesdk) jazyka Java C#
 
V případě Pythonu si stáhněte naši omezené verze z GitHubu.

1. Přejděte na [stránku verze GitHubu](https://aka.ms/vnetpythonsdk).

2. Stáhněte si následující soubor, který najdete v dolní části stránky pro vydání pod hlavičkou s názvem **assets**.
    > *azure_iot_hub-2.2.0_limited-PY2. py3-None-any. WHL*

3. Otevřete terminál a přejděte do složky se staženým souborem.

4. Spusťte následující příkaz, který nainstaluje sadu SDK služby Pythonu s podporou pro virtuální sítě:
    > instalace PIP./azure_iot_hub-2.2.0_limited-PY2. py3-None-any. WHL


## <a name="next-steps"></a>Další kroky

Další informace o funkcích IoT Hub získáte pomocí odkazů níže:

* [Směrování zpráv](./iot-hub-devguide-messages-d2c.md)
* [Nahrání souboru](./iot-hub-devguide-file-upload.md)
* [Import/export hromadného zařízení](./iot-hub-bulk-identity-mgmt.md) 
