## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Teď, když jste vytvořili centrum IoT, vyhledejte důležité informace, které používáte pro připojení zařízení a aplikací do služby IoT hub. 

1. Po vytvoření centra IoT na něj klikněte na řídicím panelu. Poznačte si **Název hostitele** a klikněte na **Zásady sdíleného přístupu**.

   ![Získání názvu hostitele pro centrum IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. V podokně **Zásady sdíleného přístupu** klikněte na zásadu **iothubowner** a potom si zkopírujte a poznamenejte **Připojovací řetězec** pro centrum IoT. Další informace najdete v tématu [Řízení přístupu ke službě IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

> [!NOTE] 
Pro tento kurz nastavení nebudete připojovací řetězec iothubowner potřebovat. Můžete ho však potřebovat pro některé kurzy k různým scénářům IoT po dokončení tohoto nastavení.

   ![Získání připojovacího řetězce centra IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registrace zařízení v centru IoT pro vaše zařízení

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT.

2. Klikněte na tlačítko **zařízení IoT**.
3. V podokně zařízení IoT, klikněte na tlačítko **přidat** pro přidání zařízení do služby IoT hub. Potom udělejte následující:

   **ID zařízení:** Zadejte ID nového zařízení. V ID zařízení se rozlišují malá a velká písmena.

   **Typ ověřování:** Vyberte **Symetrický klíč**.

   **Automaticky generovat klíče:** Zaškrtněte toto políčko.

   **Připojit zařízení k centru IoT:** Klikněte na **Povolit**.

   ![Přidání zařízení v zařízení IoT služby IoT hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-iot-devices-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Klikněte na **Uložit**.
5. Po vytvoření zařízení, otevřete v zařízení **zařízení IoT** podokně.

   ![Seznam zařízení IoT ve IoT Hub.](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_device-list-in-iot-devices-portal.png)

6. Poznamenejte si primární klíč připojovacího řetězce.

   ![Získání připojovacího řetězec zařízení](../articles/iot-hub/media/iot-hub-create-hub-and-device/8_get-device-connection-string-in-iot-devices-portal.png)
