V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **skupiny prostředků** > **cloud-shell-úložiště –\<your_region >**  >   **\<název_účtu_úložiště >**.

![Najít účet úložiště Cloud Shell](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

V **přehled** stránce účtu úložiště vyberte **soubory**.

Vyberte sdílenou složku automaticky vygenerovaný soubor a vyberte **nahrát**. V Cloud Shellu, jako je připojený tuto sdílenou složku souborového `clouddrive`.

![Najít tlačítko Nahrát](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Klepněte na volič soubor a vyberte soubor ZIP a potom klikněte na tlačítko **nahrát**. 

Ve službě Cloud Shell pomocí `ls` k ověření, že vidíte ve výchozích nahraný soubor ZIP `clouddrive` sdílet.

```azurecli-interactive
ls clouddrive
```
