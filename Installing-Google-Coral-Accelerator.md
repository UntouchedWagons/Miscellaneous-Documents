```
sudo apt purge gasket-dkms
git clone https://github.com/KyleGospo/gasket-dkms
apt install dkms libfuse2 dh-dkms devscripts build-essential debhelper
cd gasket-dkms
debuild -us -uc -tc -b
cd ..
ls *.deb
dpkg -i gasket-dkms-*.deb
sudo sh -c "echo 'SUBSYSTEM==\"apex\", MODE=\"0660\", GROUP=\"docker\"' >> /etc/udev/rules.d/65-apex.rules"
```

Taken from https://itbacon.com/2024/05/04/installing-google-coral-on-ubuntu-23-10/