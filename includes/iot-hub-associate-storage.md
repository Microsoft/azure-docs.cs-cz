## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Přidružení účtu služby Azure Storage do služby IoT Hub

Protože aplikace simulovaného zařízení nahraje soubor do objektu blob, musíte mít [služby Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) účet přidružený ke službě IoT Hub. Když přidružíte účet Azure Storage pomocí služby IoT hub, IoT hub vygeneruje identifikátor URI SAS. Zařízení můžete pomocí tohoto identifikátoru URI SAS bezpečně nahrát soubor do kontejneru objektů blob. Služba IoT Hub a sady SDK pro zařízení koordinovat proces, který vygeneruje identifikátor URI SAS a zpřístupňuje je na zařízení používat k nahrání souboru.

Postupujte podle pokynů v [nahrání souborů konfigurace pomocí webu Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md) přidružení účtu služby Azure Storage do služby IoT hub. Ujistěte se, že kontejner objektů blob souvisí s centrem IoT a že jsou povolené soubor oznámení.

![Povolit soubor oznámení na portálu](media/iot-hub-associate-storage/enable-file-notifications.png)