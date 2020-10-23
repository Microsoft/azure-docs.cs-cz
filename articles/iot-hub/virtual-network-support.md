---
title: Podpora Azure IoT Hub pro virtuální sítě
description: Použití vzoru připojení virtuálních sítí s IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 09/24/2020
ms.author: jlian
ms.openlocfilehash: cb6e4b2b10b6b44a544416ad5d57808c7ad4d83f
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427859"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Podpora IoT Hub pro virtuální sítě s privátním odkazem a spravovanou identitou

Ve výchozím nastavení se názvy hostitelů IoT Hub mapují k veřejnému koncovému bodu s veřejně směrovatelné IP adresou přes Internet. Různí zákazníci sdílí tento IoT Hub veřejný koncový bod a zařízení IoT v různých sítích a místních sítích mají přístup k nim.

![IoT Hub veřejný koncový bod](./media/virtual-network-support/public-endpoint.png)

IoT Hub funkce, včetně [směrování zpráv](./iot-hub-devguide-messages-d2c.md), [nahrávání souborů](./iot-hub-devguide-file-upload.md)a [hromadného importu a exportu zařízení](./iot-hub-bulk-identity-mgmt.md) , taky vyžadují připojení z IoT Hub k prostředku Azure vlastněné zákazníkem přes jeho veřejný koncový bod. Tyto cesty k připojení společně vytvářejí přenos z provozu od IoT Hub k zákaznickým prostředkům.

Možná budete chtít omezit připojení k prostředkům Azure (včetně IoT Hub) prostřednictvím virtuální sítě, kterou vlastníte a provozujete. Mezi tyto důvody patří:

* Představujeme izolaci sítě pro IoT Hub tím, že zabráníte vystavení připojení veřejnému Internetu.

* Díky možnosti privátního připojení z vašich místních síťových prostředků zajistíte přenos dat a přenosů přímo do páteřní sítě Azure.

* Prevence útoků exfiltrace z citlivých místních sítí. 

* Pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md)navázali vzory připojení na úrovni Azure.

Tento článek popisuje, jak dosáhnout těchto cílů pomocí [privátního propojení Azure](../private-link/private-link-overview.md) pro příchozí připojení k IoT Hub a používání důvěryhodných služeb Microsoftu pro odchozí připojení z IoT Hub na jiné prostředky Azure.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Připojení příchozího připojení k IoT Hub pomocí privátního propojení Azure

Privátní koncový bod je privátní IP adresa přidělená v rámci virtuální sítě vlastněné zákazníkem, přes kterou je prostředek Azure dostupný. Prostřednictvím privátního odkazu Azure můžete nastavit privátní koncový bod pro službu IoT Hub, abyste umožnili službám ve vaší virtuální síti přístup k IoT Hub bez nutnosti odeslání provozu do veřejného koncového bodu IoT Hub. Obdobně můžou vaše místní zařízení používat připojení [VPN (Virtual Private Network)](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](https://azure.microsoft.com/services/expressroute/) peering k získání připojení k vaší virtuální síti a k vašemu IoT Hub (prostřednictvím svého privátního koncového bodu). V důsledku toho můžete omezit nebo zcela blokovat připojení k veřejným koncovým bodům služby IoT Hub pomocí [IoT Hub filtru IP](./iot-hub-ip-filtering.md) a [konfigurací směrování Neodesílat žádná data do předdefinovaného koncového bodu](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint). Tento přístup udržuje připojení k vašemu rozbočovači pomocí privátního koncového bodu pro zařízení. Hlavním soustředěním tohoto nastavení je zařízení v místní síti. Tato instalace se nedoporučuje pro zařízení nasazená v síti se sítí na stejné úrovni.

![Engress virtuální sítě IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Než budete pokračovat, ujistěte se, že jsou splněné následující předpoklady:

* Vytvořili jste [virtuální síť Azure](../virtual-network/quick-create-portal.md) s podsítí, ve které se vytvoří privátní koncový bod.

* Pro zařízení, která fungují v místních sítích, nastavte [virtuální privátní síť (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo soukromý partnerský vztah [ExpressRoute](https://azure.microsoft.com/services/expressroute/) do virtuální sítě Azure.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Nastavení privátního koncového bodu pro IoT Hub příchozí přenosy

Privátní koncový bod funguje pro IoT Hub rozhraní API zařízení (jako jsou zprávy typu zařízení-Cloud) a také rozhraní API služby (například vytváření a aktualizace zařízení).

1. V Azure Portal vyberte **sítě**, **připojení privátních koncových bodů**a klikněte na **koncový bod + Private**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Snímek obrazovky s informacemi o tom, kam přidat privátní koncový bod pro IoT Hub":::

1. Zadejte předplatné, skupinu prostředků, název a oblast pro vytvoření nového privátního koncového bodu v nástroji. V ideálním případě by se měl privátní koncový bod vytvořit ve stejné oblasti jako vaše centrum.

1. Klikněte na **Další: prostředek**, zadejte předplatné pro svůj prostředek IoT Hub a jako typ prostředku vyberte **Microsoft. Devices/IotHubs** , jako **prostředek**IoT Hub název a jako cílový podprostředek **iotHub** .

1. Klikněte na **Další: Konfigurace** a zadejte virtuální síť a podsíť pro vytvoření privátního koncového bodu v nástroji. V případě potřeby vyberte možnost integrace s privátní zónou DNS Azure.

1. Klikněte na **Další: značky**a volitelně poskytněte pro svůj prostředek všechny značky.

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** vytvořte prostředek privátního propojení.

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>Vestavěný koncový bod kompatibilní s centrem událostí nepodporuje přístup prostřednictvím privátního koncového bodu.

[Předdefinovaný koncový bod kompatibilní](iot-hub-devguide-messages-read-builtin.md) s centrem událostí nepodporuje přístup prostřednictvím privátního koncového bodu. Po nakonfigurování je privátní koncový bod služby IoT Hub určen jenom pro připojení příchozího přenosu dat. Zpracování dat z integrovaného koncového bodu kompatibilního s centrem událostí se dá provést jenom přes veřejný Internet. 

[Filtr IP](iot-hub-ip-filtering.md) IoT Hub také neřídí veřejný přístup k předdefinovanému koncovému bodu. Pokud chcete zcela zablokovat přístup k veřejné síti ke službě IoT Hub, musíte: 

1. Konfigurace přístupu k privátnímu koncovému bodu pro IoT Hub
1. Vypnout [přístup k veřejné síti](iot-hub-public-network-access.md) nebo použít filtr IP k blokování všech IP adres
1. Zastavení používání integrovaného koncového bodu centra událostí [nastavením směrování na Neodesílat data](iot-hub-devguide-messages-d2c.md)
1. Vypnutí [záložní trasy](iot-hub-devguide-messages-d2c.md#fallback-route)
1. Konfigurace odchozího přenosu dat na jiné prostředky Azure pomocí [důvěryhodné služby Microsoftu](#egress-connectivity-from-iot-hub-to-other-azure-resources)

### <a name="pricing-for-private-link"></a>Ceny za soukromé odkazy

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Odchozí připojení z IoT Hub k jiným prostředkům Azure

IoT Hub se může připojit ke službě Azure Blob Storage, centru událostí, prostředkům sběrnice pro [směrování zpráv](./iot-hub-devguide-messages-d2c.md), [nahrávání souborů](./iot-hub-devguide-file-upload.md)a [hromadnému importu/exportu zařízení](./iot-hub-bulk-identity-mgmt.md) přes Veřejný koncový bod prostředků. Vytvoření vazby prostředku k virtuální síti ve výchozím nastavení blokují připojení k prostředku. V důsledku toho tato konfigurace brání IoT Hub v práci odesílat data do vašich prostředků. Pokud chcete tento problém vyřešit, povolte připojení z prostředku IoT Hub k účtu úložiště, centru událostí nebo prostředkům služby Service Bus prostřednictvím možnosti **Důvěryhodná služba Microsoftu** .

### <a name="turn-on-managed-identity-for-iot-hub"></a>Zapnout spravovanou identitu pro IoT Hub

Aby bylo možné ostatním službám najít službu IoT Hub jako důvěryhodnou službu Microsoftu, musí mít spravovanou identitu přiřazenou systémem.

1. Navigace na **identitu** na portálu IoT Hub

1. V části **stav**vyberte **zapnuto**a pak klikněte na **Uložit**.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Snímek obrazovky s informacemi o tom, kam přidat privátní koncový bod pro IoT Hub":::

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Přiřazení spravované identity k vašemu IoT Hub v době vytváření pomocí šablony ARM

K přiřazení spravované identity ke službě IoT Hub při zřizování prostředků použijte níže uvedenou šablonu ARM:

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

### <a name="pricing-for-managed-identity"></a>Ceny za spravovanou identitu

Důvěryhodná funkce výjimky služby Microsoft First stran je bezplatná. Poplatky za zřízené účty úložiště, centra událostí nebo prostředky služby Service Bus se vztahují samostatně.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Odchozí připojení k koncovým bodům účtu úložiště pro směrování

IoT Hub může směrovat zprávy do účtu úložiště ve vlastnictví zákazníka. Aby funkce směrování umožnila přístup k účtu úložiště, když jsou zavedena omezení brány firewall, vaše IoT Hub musí mít [spravovanou identitu](#turn-on-managed-identity-for-iot-hub). Po zřízení spravované identity použijte následující postup a udělte jí oprávnění Azure RBAC pro přístup k vašemu účtu úložiště pro vaši identitu prostředku vašeho rozbočovače.

1. V Azure Portal přejděte na kartu **řízení přístupu (IAM)** účtu úložiště a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte **Přispěvatel dat objektu BLOB úložiště** ([*ne* přispěvatel nebo přispěvatel účtu úložiště](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a v rozevíracím seznamu vyberte název prostředku IoT Hub. Klikněte na tlačítko **Uložit**.

3. V účtu úložiště přejděte na kartu **brány firewall a virtuální sítě** a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko pro **Povolení přístupu k tomuto účtu úložiště důvěryhodným službám Microsoftu**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředku IoT Hub přejděte na kartu **směrování zpráv** .

5. Přejděte do části **vlastní koncové body** a klikněte na **Přidat**. Jako typ koncového bodu vyberte **úložiště** .

6. Na stránce, která se zobrazí, zadejte název koncového bodu, vyberte kontejner, který chcete použít ve službě BLOB Storage, zadejte kódování a formát názvu souboru. Jako **typ ověřování** vyberte na **základě identity** na koncový bod úložiště. Klikněte na tlačítko **Vytvořit**.

Vlastní koncový bod úložiště je teď nastavený tak, aby používal identitu přiřazenou systémem vašeho rozbočovače a měl oprávnění pro přístup k vašemu prostředku úložiště Navzdory omezením brány firewall. Nyní můžete použít tento koncový bod k nastavení pravidla směrování.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Odchozí připojení k koncovým bodům centra událostí pro směrování

IoT Hub je možné nakonfigurovat tak, aby směroval zprávy do oboru názvů centra událostí vlastněných zákazníkem. Aby funkce směrování umožnila přístup k prostředku centra událostí, zatímco jsou zavedena omezení brány firewall, IoT Hub musí mít spravovanou identitu. Po vytvoření spravované identity postupujte podle následujících kroků a udělte službě Azure RBAC oprávnění k přístupu k vašemu centru událostí.

1. V Azure Portal přejděte na kartu IAM (Event hub **Access Control)** a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte **Event Hubs datový odesílatel** jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a vyberte název prostředku IoT Hub v rozevíracím seznamu. Klikněte na tlačítko **Uložit**.

3. Na kartě centra událostí přejděte na kartu **brány firewall a virtuální sítě** a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko, aby **důvěryhodné služby Microsoftu měly přístup k**centrům událostí. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředku IoT Hub přejděte na kartu **směrování zpráv** .

5. Přejděte do části **vlastní koncové body** a klikněte na **Přidat**. Jako typ koncového bodu vyberte možnost **centra událostí** .

6. Na stránce, která se zobrazí, zadejte název koncového bodu, vyberte obor názvů a instanci centra událostí a klikněte na tlačítko **vytvořit** .

Vaše vlastní koncový bod centra událostí je teď nastavený tak, aby používal identitu přiřazenou systémem vašeho centra, a má oprávnění k přístupu k vašemu prostředku centra událostí bez ohledu na jeho omezení brány firewall. Nyní můžete použít tento koncový bod k nastavení pravidla směrování.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Odchozí připojení ke koncovým bodům služby Service Bus pro směrování

IoT Hub je možné nakonfigurovat tak, aby směroval zprávy do oboru názvů služby Service Bus vlastněné zákazníkem. Aby funkce směrování umožnila přístup k prostředku služby Service Bus, když jsou zavedena omezení brány firewall, vaše IoT Hub musí mít spravovanou identitu. Po zřízení spravované identity postupujte podle následujících kroků a udělte službě Azure RBAC oprávnění ke svojí identitě prostředků vašeho rozbočovače pro přístup k vaší službě Service Bus.

1. V Azure Portal přejděte na kartu **řízení přístupu (IAM)** služby Service Bus a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte možnost **odesílatel dat Service Bus** jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a v rozevíracím seznamu vyberte název prostředku IoT Hub. Klikněte na tlačítko **Uložit**.

3. Přejděte na kartu **brány firewall a virtuální sítě** ve službě Service Bus a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko pro **Povolení přístupu ke službě Service Bus důvěryhodným službám Microsoftu**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředku IoT Hub přejděte na kartu **směrování zpráv** .

5. Přejděte do části **vlastní koncové body** a klikněte na **Přidat**. Jako typ koncového bodu vyberte položku **fronta služby Service Bus** nebo **Service Bus téma** (podle potřeby).

6. Na stránce, která se zobrazí, zadejte název koncového bodu, vyberte obor názvů služby Service Bus a frontu nebo téma (podle potřeby). Klikněte na tlačítko **Vytvořit**.

Vlastní koncový bod služby Service Bus je teď nastavený tak, aby používal identitu přiřazenou systémem vašeho rozbočovače a měl oprávnění k přístupu k prostředku služby Service Bus Navzdory omezením brány firewall. Nyní můžete použít tento koncový bod k nastavení pravidla směrování.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Odchozí připojení k účtům úložiště pro nahrání souboru

Funkce nahrávání souborů IoT Hub umožňuje zařízením nahrávat soubory do účtu úložiště ve vlastnictví zákazníka. Aby mohl nahrávání souboru fungovat, musí mít obě zařízení i IoT Hub připojení k účtu úložiště. Pokud jsou na účtu úložiště zavedena omezení brány firewall, musí vaše zařízení získat připojení pomocí některého z podporovaných mechanismů účtu úložiště (včetně [privátních koncových bodů](../private-link/tutorial-private-endpoint-storage-portal.md), [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md)nebo [přímé konfigurace brány firewall](../storage/common/storage-network-security.md)). Podobně platí, že pokud jsou na účtu úložiště zavedena omezení brány firewall, IoT Hub musí být nakonfigurovaná pro přístup k prostředku úložiště přes důvěryhodnou výjimku služby Microsoftu. Pro tento účel IoT Hub nutné mít spravovanou identitu. Po zřízení spravované identity použijte následující postup a udělte jí oprávnění Azure RBAC pro přístup k vašemu účtu úložiště pro vaši identitu prostředku vašeho rozbočovače.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. V Azure Portal přejděte na kartu **řízení přístupu (IAM)** účtu úložiště a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte **Přispěvatel dat objektu BLOB úložiště** ([*ne* přispěvatel nebo přispěvatel účtu úložiště](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a v rozevíracím seznamu vyberte název prostředku IoT Hub. Klikněte na tlačítko **Uložit**.

3. V účtu úložiště přejděte na kartu **brány firewall a virtuální sítě** a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko pro **Povolení přístupu k tomuto účtu úložiště důvěryhodným službám Microsoftu**. Klikněte na tlačítko **Uložit**.

4. Na stránce prostředku IoT Hub přejděte na kartu **nahrávání souborů** .

5. Na stránce, která se zobrazí, vyberte kontejner, který chcete použít ve svém úložišti objektů blob, nakonfigurujte **Nastavení oznamování souborů**, **TTL SAS**, **výchozí hodnotu TTL**a **maximální počet doručení** podle potřeby. Jako **typ ověřování** vyberte na **základě identity** na koncový bod úložiště. Klikněte na tlačítko **Vytvořit**. Pokud se v tomto kroku zobrazí chyba, dočasně nastavte účet úložiště tak, aby povoloval přístup ze **všech sítí**, a pak to zkuste znovu. Po dokončení konfigurace nahrávání souboru můžete na účtu úložiště nakonfigurovat bránu firewall.

Teď je koncový bod úložiště pro nahrání souborů nastavený tak, aby používal identitu přiřazenou systémem vašeho rozbočovače a měl oprávnění pro přístup k vašemu prostředku úložiště Navzdory omezením brány firewall.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Odchozí připojení k účtům úložiště pro import/export hromadného zařízení

IoT Hub podporuje funkce pro [Import/export](./iot-hub-bulk-identity-mgmt.md) informací o zařízeních do objektu BLOB úložiště poskytovaného zákazníkem hromadně. Aby funkce hromadného importu a exportu mohla fungovat, musí mít obě zařízení i IoT Hub připojení k účtu úložiště.

Tato funkce vyžaduje připojení z IoT Hub k účtu úložiště. Aby bylo možné získat přístup k prostředku služby Service Bus, pokud jsou k dispozici omezení brány firewall, vaše IoT Hub musí mít spravovanou identitu. Po zřízení spravované identity postupujte podle následujících kroků a udělte službě Azure RBAC oprávnění ke svojí identitě prostředků vašeho rozbočovače pro přístup k vaší službě Service Bus.

1. V Azure Portal přejděte na kartu **řízení přístupu (IAM)** účtu úložiště a v části **Přidat přiřazení role** klikněte na **Přidat** .

2. Vyberte **Přispěvatel dat objektu BLOB úložiště** ([*ne* přispěvatel nebo přispěvatel účtu úložiště](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) jako **role**, **uživatel služby Azure AD, skupinu nebo instanční objekt,** jako **přiřazení přístupu k** a v rozevíracím seznamu vyberte název prostředku IoT Hub. Klikněte na tlačítko **Uložit**.

3. V účtu úložiště přejděte na kartu **brány firewall a virtuální sítě** a povolte možnost **Povolit přístup z vybraných sítí** . V seznamu **výjimek** zaškrtněte políčko pro **Povolení přístupu k tomuto účtu úložiště důvěryhodným službám Microsoftu**. Klikněte na tlačítko **Uložit**.

Teď můžete použít rozhraní REST API Azure IoT pro [vytváření úloh importu exportu](/rest/api/iothub/service/jobs/getimportexportjobs) , kde najdete informace o tom, jak používat funkce hromadného importu a exportu. V textu žádosti budete muset zadat a `storageAuthenticationType="identityBased"` použít `inputBlobContainerUri="https://..."` a `outputBlobContainerUri="https://..."` jako vstupní a výstupní adresy URL vašeho účtu úložiště.

Sady SDK pro Azure IoT Hub podporují tuto funkci také ve Správci registru klienta služby. Následující fragment kódu ukazuje, jak iniciovat úlohu importu nebo úlohu exportu v rámci používání sady C# SDK.

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

Použití této verze sad SDK Azure IoT s podporou virtuální sítě pro C#, Java a Node.js:

1. Vytvořte proměnnou prostředí s názvem `EnableStorageIdentity` a nastavte její hodnotu na `1` .

2. Stažení sady SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk) Java C#
 
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