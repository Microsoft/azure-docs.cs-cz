---
title: Podpora Azure IoT Hubu pro virtuální sítě
description: Jak používat způsob připojení virtuálních sítí pomocí služby IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501432"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Podpora služby IoT Hub pro virtuální sítě

Tento článek představuje vzor připojení virtuální sítě a zpracovává, jak nastavit prostředí privátní připojení k centru IoT prostřednictvím virtuální sítě Azure vlastněné zákazníkem.

> [!NOTE]
> Funkce služby IoT Hub popsané v tomto článku jsou aktuálně k dispozici pro centra IoT [vytvořené s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity) v následujících oblastech: Východní USA, – střed usa a ZÁPADNÍ USA 2.


## <a name="introduction"></a>Úvod

Ve výchozím nastavení jsou názvy hostitelů centra IoT Hub mapovány na veřejný koncový bod s veřejně směrovatelnou IP adresou přes Internet. Jak je znázorněno na obrázku níže, tento veřejný koncový bod ioT hubu je sdílen mezi rozbočovači vlastněnými různými zákazníky a k němu mají přístup zařízení IoT v rozsáhlých sítích i v místních sítích.

Několik funkcí služby IoT Hub, včetně [směrování zpráv](./iot-hub-devguide-messages-d2c.md), [nahrávání souborů](./iot-hub-devguide-file-upload.md)a [hromadného importu a exportu zařízení,](./iot-hub-bulk-identity-mgmt.md) podobně vyžaduje připojení z IoT Hubu k prostředku Azure vlastněného zákazníkem přes jeho veřejný koncový bod. Jak je znázorněno níže, tyto cesty připojení společně představují odchozí provoz z centra IoT Hub do prostředků zákazníků.
![Veřejný koncový bod ioT Hubu](./media/virtual-network-support/public-endpoint.png)


Z několika důvodů mohou zákazníci chtít omezit připojení ke svým prostředkům Azure (včetně Služby IoT Hub) prostřednictvím virtuální sítě, kterou vlastní a provozují. Mezi tyto důvody patří:

* Zavedení dalších vrstev zabezpečení prostřednictvím izolace na úrovni sítě pro vaše služby IoT hub tím, že zabráníte vystavení připojení k vašemu centru přes veřejný Internet.

* Povolení prostředí privátního připojení z prostředků místní sítě zajišťující, že vaše data a provoz se přenášejí přímo do páteřní sítě Azure.

* Zabránění exfiltračním útokům z citlivých místních sítí. 

* Po zavedených vzorech připojení pro celý Azure pomocí [privátní koncové body](../private-link/private-endpoint-overview.md).


Tento článek popisuje, jak dosáhnout těchto cílů pomocí [privátní koncové body](../private-link/private-endpoint-overview.md) pro příchozí přenos dat připojení k ioT hub, jako použití azure důvěryhodné služby první strany výjimky pro odchozí připojení z ioT hubu do jiných prostředků Azure.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Příchozí přenos dat připojení k centru IoT Hub pomocí privátní koncové body

Privátní koncový bod je privátní IP adresa přidělená uvnitř virtuální sítě vlastněné zákazníkem, přes kterou je dostupný prostředek Azure. Tím, že máte soukromý koncový bod pro vaše centrum IoT, budete moct povolit služby operující uvnitř vaší virtuální sítě k dosažení služby IoT Hub bez nutnosti přenosu odeslat do veřejného koncového bodu služby IoT Hub. Podobně zařízení, která pracují ve vašem místním prostředí, můžou používat [virtuální privátní síť (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) nebo privátní partnerský vztah [ExpressRoute](https://azure.microsoft.com/services/expressroute/) k získání připojení k virtuální síti v Azure a následně k vašemu centru IoT Hub (prostřednictvím privátního koncového bodu). V důsledku toho zákazníci, kteří chtějí omezit připojení k veřejným koncovým bodům svého centra IoT hub (nebo jej případně úplně zablokovat), mohou tohoto cíle dosáhnout pomocí [pravidel brány firewall služby IoT Hub](./iot-hub-ip-filtering.md) při zachování připojení k jejich centru pomocí privátního koncového bodu.

> [!NOTE]
> Toto nastavení se zaměřuje především na zařízení v místní síti. Toto nastavení se nedoporučuje pro zařízení nasazená v síti s širokou oblastí.

![Veřejný koncový bod ioT Hubu](./media/virtual-network-support/virtual-network-ingress.png)

Před pokračováním ujistěte se, že jsou splněny následující předpoklady:

* Vaše centrum IoT musí být zřízené s [identitou spravované služby](#create-an-iot-hub-with-managed-service-identity).

* Vaše centrum IoT musí být zřízeno v jedné z [podporovaných oblastí](#regional-availability-private-endpoints).

* Zřídíte virtuální síť Azure s podsítí, ve které se vytvoří privátní koncový bod. Další podrobnosti najdete [v tématu vytvoření virtuální sítě pomocí azure cli.](../virtual-network/quick-create-cli.md)

* Pro zařízení, která pracují uvnitř místních sítí, nastavte [virtuální privátní síť (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) nebo [expressroute](https://azure.microsoft.com/services/expressroute/) privátní partnerský vztah do virtuální sítě Azure.


### <a name="regional-availability-private-endpoints"></a>Regionální dostupnost (soukromé koncové body)

Privátní koncové body podporované v ioT hubu vytvořené v následujících oblastech:

* USA – východ

* USA – středojih

* USA – západ 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Nastavení privátního koncového bodu pro příchozí přenos dat služby IoT Hub

Chcete-li nastavit soukromý koncový bod, postupujte takto:

1. Spusťte následující příkaz y Azure CLI a znovu zaregistrujte poskytovatele azure iot hubu s vaším předplatným:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Přejděte na kartu **Soukromá připojení koncových bodů** na portálu Služby IoT Hub (tato karta **+** je dostupná jenom v rozbočovačích IoT hub v [podporovaných oblastech](#regional-availability-private-endpoints)) a kliknutím na znaménko přidejte nový soukromý koncový bod.

3. Zadejte předplatné, skupinu prostředků, název a oblast k vytvoření nového privátního koncového bodu v aplikaci (v ideálním případě by měl být soukromý koncový bod vytvořen ve stejné oblasti jako vaše centrum; další podrobnosti najdete v [části místní dostupnosti).](#regional-availability-private-endpoints)

4. Klikněte na **další: Prostředek**a zadejte předplatné pro váš prostředek služby IoT Hub a vyberte **"Microsoft.Devices/IotHubs"** jako typ prostředku, název služby IoT Hub jako **prostředek**a **iotHub** jako cílový dílčí prostředek.

5. Klikněte na **Další: Konfigurace** a zadejte virtuální síť a podsíť pro vytvoření privátního koncového bodu. Vyberte možnost integrace s privátní zónou DNS Azure, pokud je to žádoucí.

6. Klikněte na **Další: Značky**a volitelně poskytněte všechny značky pro váš prostředek.

7. Kliknutím na **Revize + vytvoření** vytvořte soukromý prostředek koncového bodu.


### <a name="pricing-private-endpoints"></a>Ceny (soukromé koncové body)

Podrobnosti o cenách najdete v článku [Ceny Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Odchozí připojení z IoT Hubu do jiných prostředků Azure

IoT Hub potřebuje přístup k úložišti objektů blob Azure, centra událostí, service bus prostředky pro [směrování zpráv](./iot-hub-devguide-messages-d2c.md), [nahrávání souborů](./iot-hub-devguide-file-upload.md)a hromadné zařízení import [nebo export](./iot-hub-bulk-identity-mgmt.md), který obvykle probíhá přes veřejný koncový bod prostředků. V případě, že svážete účet úložiště, centra událostí nebo prostředek sběrnice služby s virtuální sítí, doporučená konfigurace ve výchozím nastavení zablokuje připojení k prostředku. V důsledku toho to bude bránit ioT Hub funkce, která vyžaduje přístup k těmto prostředkům.

Chcete-li tuto situaci zmírnit, musíte povolit připojení z prostředku služby IoT Hub k vašemu účtu úložiště, rozbočovačům událostí nebo prostředkům služby Service Bus prostřednictvím možnosti **důvěryhodných služeb Azure první strany.**

Požadavky jsou následující:

* Vaše centrum IoT musí být zřízeno v jedné z [podporovaných oblastí](#regional-availability-trusted-microsoft-first-party-services).

* Vaše služba IoT Hub musí být přiřazena identita spravované služby v době zřizování centra. Postupujte podle pokynů o tom, jak [vytvořit rozbočovač s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Regionální dostupnost (důvěryhodné služby první strany společnosti Microsoft)

Výjimka služeb azure trusted first party, která obcupuje omezení brány firewall pro úložiště Azure, centra událostí a prostředky služby Service Bus, je podporovaná jenom pro centra IoT Hub v následujících oblastech:

* USA – východ

* USA – středojih

* USA – západ 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Ceny (důvěryhodné služby první strany společnosti Microsoft)

Funkce výjimky služeb důvěryhodné společnosti Microsoft první strany je v službách IoT Hub v [podporovaných oblastech](#regional-availability-trusted-microsoft-first-party-services)bezplatná. Poplatky za zřízené účty úložiště, centra událostí nebo prostředky služby Service Bus platí samostatně.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Vytvoření centra IoT s identitou spravované služby

Identitu spravované služby lze přiřadit k vašemu centru v době zřizování prostředků (tato funkce není aktuálně podporována pro existující centra), což vyžaduje, aby centrum IoT používalo TLS 1.2 jako minimální verzi. Pro tento účel je třeba použít níže uvedenou šablonu prostředků ARM:

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

Po `name`nahrazení hodnot pro váš prostředek , `location` `SKU.name` a `SKU.tier`, můžete použít Azure CLI k nasazení prostředku v existující skupině prostředků pomocí:

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Po vytvoření prostředku můžete načíst identitu spravované služby přiřazenou k vašemu rozbočovači pomocí azure cli:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Po zřízení služby IoT Hub s identitou spravované služby postupujte podle odpovídající části a nastavte koncové body směrování na [účty úložiště](#egress-connectivity-to-storage-account-endpoints-for-routing), centra událostí a prostředky [služby](#egress-connectivity-to-event-hubs-endpoints-for-routing) [Service Bus](#egress-connectivity-to-service-bus-endpoints-for-routing) nebo nakonfigurujte import a export zařízení pro [odesílání souborů](#egress-connectivity-to-storage-accounts-for-file-upload) a [hromadné zařízení](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport).


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Odchozí připojení ke koncovým bodům účtu úložiště pro směrování

Službu IoT Hub lze nakonfigurovat tak, aby směrovala zprávy na účet úložiště vlastněného zákazníkem. Chcete-li povolit funkci směrování pro přístup k účtu úložiště, zatímco omezení brány firewall jsou na místě, vaše Služba IoT Hub musí mít identitu spravované služby (přečtěte si, jak [vytvořit rozbočovač s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity)). Po zřízení identity spravované služby udělit RBAC oprávnění k identitě prostředku vašeho centra pro přístup k účtu úložiště.

1. Na webu Azure Portal přejděte na kartu **řízení přístupu (IAM)** vašeho účtu úložiště a klikněte na **Přidat** v části **Přidat přiřazení role.**

2. Vyberte **přispěvatel dat objektů blob úložiště** jako **roli**, uživatele Azure **AD, skupinu nebo instanční objekt** jako **Přiřazení přístupu** k a vyberte název prostředku služby IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. Přejděte na kartu **Brány firewall a virtuální sítě** v účtu úložiště a povolte možnost **Povolit přístup z vybraných sítí.** V seznamu **Výjimky** zaškrtněte políčko **Povolit důvěryhodným službám společnosti Microsoft přístup k tomuto účtu úložiště**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředků služby IoT Hub přejděte na kartu **Směrování zpráv.**

5. Přejděte do části **Vlastní koncové body** a klepněte na tlačítko **Přidat**. Jako typ koncového bodu vyberte **Úložiště.**

6. Na stránce, která se zobrazí, zadejte název koncového bodu, vyberte kontejner, který chcete použít v úložišti objektů blob, zadejte kódování a formát názvu souboru. Vyberte Typ **ověřování** **přiřazený** k koncovému bodu úložiště. Klikněte na tlačítko **Vytvořit**.

Nyní je váš vlastní koncový bod úložiště nastaven tak, aby používal identitu přiřazenou systému vašeho centra, a má oprávnění k přístupu k prostředkům úložiště navzdory omezením brány firewall. Tento koncový bod můžete nyní použít k nastavení pravidla směrování.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Odchozí připojení ke koncovým bodům centra událostí pro směrování

Službu IoT Hub lze nakonfigurovat tak, aby směrovala zprávy do oboru názvů center událostí vlastněných zákazníkem. Chcete-li povolit funkci směrování pro přístup k prostředku centra událostí, zatímco jsou zavedena omezení brány firewall, musí mít vaše centrum IoT Hub identitu spravované služby (přečtěte si, jak [vytvořit rozbočovač s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity)). Po zřízení identity spravované služby postupujte podle následujících kroků a udělit RBAC oprávnění k identitě prostředků vašeho centra pro přístup k vašim centrům událostí.

1. Na webu Azure Portal přejděte na kartu Řízení přístupu k centru událostí **(IAM)** a klikněte na **Přidat** v části **Přidat přiřazení role.**

2. Vyberte **Data Sender centra událostí** jako **role**, **uživatel, skupina azure nebo instanční objekt** jako Přiřazení **přístupu** k a vyberte název prostředku služby IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. Přejděte na kartu **Brány firewall a virtuální sítě** v rozbočovačích událostí a povolte možnost **Povolit přístup z vybraných sítí.** V seznamu **Výjimky** zaškrtněte políčko **Povolit důvěryhodným službám Společnosti Microsoft přístup k rozbočovačům událostí**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředků služby IoT Hub přejděte na kartu **Směrování zpráv.**

5. Přejděte do části **Vlastní koncové body** a klepněte na tlačítko **Přidat**. Jako typ koncového bodu vyberte **Centra událostí.**

6. Na stránce, která se zobrazí, zadejte název koncového bodu, vyberte obor názvů a instance centra událostí a klikněte na tlačítko **Vytvořit.**

Nyní je koncový bod vlastních center událostí nastaven tak, aby používal přiřazenou identitu vašeho centra a má oprávnění k přístupu k prostředku centra událostí navzdory omezením brány firewall. Tento koncový bod můžete nyní použít k nastavení pravidla směrování.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Odchozí připojení ke koncovým bodům sběrnice pro směrování

Službu IoT Hub lze nakonfigurovat tak, aby směrovala zprávy do oboru názvů servisní chodníče vlastněného zákazníkem. Chcete-li povolit funkci směrování pro přístup k prostředku služby service bus, zatímco jsou zavedena omezení brány firewall, musí mít služba IoT Hub identitu spravované služby (viz jak [vytvořit rozbočovač s identitou spravované služby](#create-an-iot-hub-with-managed-service-identity)). Po zřízení identity spravované služby postupujte podle následujících kroků a udělit rbac oprávnění k identitě prostředku vašeho centra pro přístup k vaší sběrnici.

1. Na webu Azure Portal přejděte na kartu **řízení přístupu (IAM)** vaší služby Service Bus a klikněte na **Přidat** v části **Přidat přiřazení role.**

2. Vyberte **Service bus Data Sender** jako **role**, Uživatel **Azure AD, skupina nebo instanční objekt** jako Přiřazení **přístupu** a vyberte název prostředku služby IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. Přejděte na kartu **Brány firewall a virtuální sítě** v sběrnici a povolte možnost **Povolit přístup z vybraných sítí.** V seznamu **Výjimky** zaškrtněte políčko **Povolit důvěryhodným službám společnosti Microsoft přístup k této sběrnici**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředků služby IoT Hub přejděte na kartu **Směrování zpráv.**

5. Přejděte do části **Vlastní koncové body** a klepněte na tlačítko **Přidat**. Jako typ koncového bodu vyberte téma **fronty sběrnice** nebo **služby Service Bus** (podle potřeby).

6. Na stránce, která se zobrazí, zadejte název koncového bodu, vyberte obor názvů a fronty nebo tématu služby Service Bus (podle potřeby). Klikněte na tlačítko **Vytvořit**.

Nyní je váš vlastní koncový bod služby Service Bus nastaven tak, aby používal přiřazenou identitu systému vašeho centra, a má oprávnění k přístupu k prostředku služby Service Bus navzdory omezením brány firewall. Tento koncový bod můžete nyní použít k nastavení pravidla směrování.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Odchozí připojení k účtům úložiště pro odeslání souboru

Funkce nahrávání souborů služby IoT Hub umožňuje zařízením nahrávat soubory do účtu úložiště vlastněného zákazníkem. Aby bylo možné nahrávání souborů fungovat, musí mít zařízení i službu IoT Hub připojení k účtu úložiště. Pokud jsou v účtu úložiště zavedena omezení brány firewall, musí vaše zařízení k získání připojení použít libovolný mechanismus podporovaného účtu úložiště (včetně [privátních koncových bodů](../private-link/create-private-endpoint-storage-portal.md), [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md) nebo [konfigurace přímé brány firewall).](../storage/common/storage-network-security.md) Podobně pokud jsou v účtu úložiště zavedena omezení brány firewall, je třeba službu IoT Hub nakonfigurovat pro přístup k prostředku úložiště prostřednictvím výjimky důvěryhodných služeb společnosti Microsoft. Za tímto účelem musí mít vaše služba IoT Hub identitu spravované služby (viz postup [vytvoření centra s identitou spravované služby).](#create-an-iot-hub-with-managed-service-identity) Po zřízení identity spravované služby udělit RBAC oprávnění k identitě prostředku vašeho centra pro přístup k účtu úložiště.

1. Na webu Azure Portal přejděte na kartu **řízení přístupu (IAM)** vašeho účtu úložiště a klikněte na **Přidat** v části **Přidat přiřazení role.**

2. Vyberte **přispěvatel dat objektů blob úložiště** jako **roli**, uživatele Azure **AD, skupinu nebo instanční objekt** jako **Přiřazení přístupu** k a vyberte název prostředku služby IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. Přejděte na kartu **Brány firewall a virtuální sítě** v účtu úložiště a povolte možnost **Povolit přístup z vybraných sítí.** V seznamu **Výjimky** zaškrtněte políčko **Povolit důvěryhodným službám společnosti Microsoft přístup k tomuto účtu úložiště**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředků ioT hubu přejděte na kartu **Nahrání souborů.**

5. Na stránce, která se zobrazí, vyberte kontejner, který chcete použít v úložišti objektů blob, nakonfigurujte **nastavení oznámení souboru**, **SAS TTL**, **Výchozí TTL** a **Maximální počet doručení** podle potřeby. Vyberte Typ **ověřování** **přiřazený** k koncovému bodu úložiště. Klikněte na tlačítko **Vytvořit**.

Nyní je koncový bod úložiště pro nahrávání souborů nastaven tak, aby používal identitu přiřazenou systému vašeho centra, a má oprávnění k přístupu k vašemu prostředku úložiště navzdory omezením brány firewall.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Odchozí připojení k účtům úložiště pro import a export zařízení s hromadným přenosem

IoT Hub podporuje funkce [importu a exportu](./iot-hub-bulk-identity-mgmt.md) informací zařízení hromadně z/do objektu blob úložiště poskytovaného zákazníkem. Aby funkce hromadného importu a exportu fungovala, musí mít zařízení i službu IoT Hub připojení k účtu úložiště.

Tato funkce vyžaduje připojení z ioT hubu k účtu úložiště. Chcete-li získat přístup k prostředku služby Service Bus, zatímco jsou zavedena omezení brány firewall, musí mít vaše služba IoT Hub identitu spravované služby (přečtěte si, jak [vytvořit centrum s identitou spravované služby).](#create-an-iot-hub-with-managed-service-identity) Po zřízení identity spravované služby postupujte podle následujících kroků a udělit rbac oprávnění k identitě prostředku vašeho centra pro přístup k vaší sběrnici.

1. Na webu Azure Portal přejděte na kartu **řízení přístupu (IAM)** vašeho účtu úložiště a klikněte na **Přidat** v části **Přidat přiřazení role.**

2. Vyberte **přispěvatel dat objektů blob úložiště** jako **roli**, uživatele Azure **AD, skupinu nebo instanční objekt** jako **Přiřazení přístupu** k a vyberte název prostředku služby IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. Přejděte na kartu **Brány firewall a virtuální sítě** v účtu úložiště a povolte možnost **Povolit přístup z vybraných sítí.** V seznamu **Výjimky** zaškrtněte políčko **Povolit důvěryhodným službám společnosti Microsoft přístup k tomuto účtu úložiště**. Klikněte na tlačítko **Uložit**.

Teď můžete použít rozhraní Azure IoT REST API pro [vytváření úloh importu exportu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) pro informace o tom, jak používat funkci hromadného importu a exportu. Všimněte si, že `storageAuthenticationType="identityBased"` budete muset poskytnout `inputBlobContainerUri="https://..."` v `outputBlobContainerUri="https://..."` těle požadavku a použití a jako vstupní a výstupní adresy URL vašeho účtu úložiště, resp.


Azure IoT Hub SDK také podporují tuto funkci ve správci registru klienta služby. Následující fragment kódu ukazuje, jak iniciovat úlohu importu nebo exportovat úlohu pomocí sady C# SDK.

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


Použití této verze sad Azure IoT SDK s omezenou oblastí s podporou virtuální sítě pro jazyk C#, Java a Node.js:

1. Vytvořte proměnnou `EnableStorageIdentity` prostředí s `1`názvem a nastavte její hodnotu na .

2. Stažení sady SDK: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Pro Python si stáhněte naši omezenou verzi z GitHubu.

1. Přejděte na [stránku vydání GitHubu](https://aka.ms/vnetpythonsdk).

2. Stáhněte si následující soubor, který najdete v dolní části stránky vydání pod záhlavím s názvem **assets**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Otevřete terminál a přejděte do složky se staženým souborem.

4. Spuštěním následujícího příkazu nainstalujte sadu Python Service SDK s podporou virtuálních sítí:
    > pip instalace ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Další kroky

Další informace o funkcích IoT Hubu najdete v následujících odkazech:

* [Směrování zpráv](./iot-hub-devguide-messages-d2c.md)
* [Nahrání souboru](./iot-hub-devguide-file-upload.md)
* [Hromadný import/export zařízení](./iot-hub-bulk-identity-mgmt.md) 
