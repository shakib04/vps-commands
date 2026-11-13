# Solve the `rtlwifi: AP off, try to reconnect now` Issue in Ubuntu

Ubuntu PC বা ল্যাপটপে Wi-Fi বারবার Disconnect হওয়া, `rtlwifi: AP off, try to reconnect now` Error দেখানো কিংবা Wi-Fi সমস্যার কারণে সিস্টেম Hang বা Freeze হওয়া সমাধানের জন্য নিচের ধাপগুলো অনুসরণ করুন।

## Step 1: Wi-Fi Adapter-এর সঠিক মডেল শনাক্ত করা

পিসি চালু হওয়ার পর Terminal ওপেন করে নিচের কমান্ডটি চালান:

```bash
lspci -nnk | grep -i net -A3
```

**সতর্কতা:** কমান্ডটি সঠিকভাবে কপি করে ব্যবহার করুন।

Output-এ `Kernel modules:` এর পাশে আপনার Wi-Fi কার্ডের মডেল দেখতে পাবেন।

উদাহরণ:
- `rtl8723be`
- `rtl8188ee`

এই গাইডটি বিশেষভাবে **Realtek RTL8723BE** Wi-Fi Adapter-এর জন্য তৈরি।

## Step 2: Wi-Fi Driver-এর Power Saving বন্ধ করা এবং Antenna Configuration ঠিক করা

HP ল্যাপটপের Realtek RTL8723BE Wi-Fi Adapter-এ Power Management এবং Antenna Configuration-এর কারণে Wi-Fi Disconnect হওয়ার সমস্যা দেখা দিতে পারে।

সমস্যাটি সমাধানের জন্য Terminal-এ নিচের কমান্ডটি চালান:

```bash
echo "options rtl8723be fwlps=0 ips=0 swenc=1 ant_sel=2" | sudo tee /etc/modprobe.d/rtl8723be.conf
```

পাসওয়ার্ড চাইলে দিয়ে Enter চাপুন।

**কমান্ডের Parameter-গুলোর কাজ:**

- `fwlps=0`: Firmware-Level Power Saving বন্ধ করে।
- `ips=0`: Inactive Power Saving বন্ধ করে।
- `swenc=1`: Hardware Encryption-এর পরিবর্তে Software Encryption ব্যবহার করে।
- `ant_sel=2`: Wi-Fi Adapter-এর দ্বিতীয় Antenna Port নির্বাচন করে।

**দ্রষ্টব্য:** যদি Wi-Fi Signal দুর্বল থাকে, তাহলে `ant_sel=2` এর পরিবর্তে `ant_sel=1` ব্যবহার করে পরীক্ষা করতে পারেন। সঠিক Antenna Port ল্যাপটপের Hardware Configuration-এর ওপর নির্ভর করে।

## Step 3: Kernel-Level PCIe Power Management বন্ধ করা

কিছু ক্ষেত্রে PCIe Active State Power Management (ASPM) এবং Wi-Fi Adapter-এর মধ্যে Compatibility সমস্যা দেখা দিতে পারে।

এটি পরীক্ষা করার জন্য GRUB Configuration-এ `pcie_aspm=off` Kernel Parameter যুক্ত করুন।

Terminal-এ চালান:

```bash
sudo sed -i 's/quiet splash/quiet splash pcie_aspm=off/g' /etc/default/grub
```

এই Parameter PCIe Power Saving বন্ধ করে দেয়।

**সতর্কতা:** এটি সিস্টেমের Power Consumption কিছুটা বাড়াতে পারে। এছাড়া কমান্ডটি GRUB-এর প্রতিটি `quiet splash` occurrence পরিবর্তন করতে পারে। আগে থেকে একই Parameter যুক্ত আছে কি না পরীক্ষা করুন।

## Step 4: GRUB Update এবং System Restart

উপরের পরিবর্তনগুলো কার্যকর করার জন্য নিচের কমান্ড দুটি একে একে চালান।

**১. GRUB Configuration Update:**

```bash
sudo update-grub
```

**২. System Restart:**

```bash
sudo reboot
```

**দ্রষ্টব্য:** `sudo update-grub` Ubuntu ও Debian-ভিত্তিক ডিস্ট্রিবিউশনের জন্য। Arch Linux বা Fedora-এর মতো অন্য ডিস্ট্রিবিউশনে নিজস্ব GRUB Update পদ্ধতি ব্যবহার করতে হবে।

## Step 5: সমস্যাটি সমাধান হয়েছে কি না যাচাই করা

সিস্টেম Restart হওয়ার পর নিচের বিষয়গুলো পরীক্ষা করুন:

- [ ] Ubuntu স্বাভাবিকভাবে Boot হচ্ছে কি না।
- [ ] Wi-Fi Adapter সঠিকভাবে কাজ করছে কি না।
- [ ] Wi-Fi Connection বারবার Disconnect হচ্ছে কি না।
- [ ] `rtlwifi: AP off, try to reconnect now` Error আবার দেখা যাচ্ছে কি না।
- [ ] Keyboard ও Mouse Freeze হচ্ছে কি না।
- [ ] Wi-Fi Signal Strength স্বাভাবিক আছে কি না।

## Troubleshooting

যদি Wi-Fi Signal দুর্বল থাকে, তাহলে Antenna Configuration পরিবর্তন করে পরীক্ষা করুন।

**প্রথমে Configuration File খুলুন:**

```bash
sudo nano /etc/modprobe.d/rtl8723be.conf
```

**বর্তমান Configuration:**

```bash
options rtl8723be fwlps=0 ips=0 swenc=1 ant_sel=2
```

**পরিবর্তিত Configuration:**

```bash
options rtl8723be fwlps=0 ips=0 swenc=1 ant_sel=1
```

পরিবর্তন Save করে বের হয়ে System Restart দিন:

```bash
sudo reboot
```

তারপর Wi-Fi Signal Strength এবং Connection Stability পরীক্ষা করুন।

---

**গুরুত্বপূর্ণ:** এই সমাধানটি Realtek RTL8723BE Adapter-এর জন্য নির্দিষ্ট। অন্য Realtek Adapter-এর ক্ষেত্রে একই Driver Parameter ব্যবহার করা যাবে না। সমস্যা অব্যাহত থাকলে নিচের কমান্ড দিয়ে Kernel Log পরীক্ষা করুন:

```bash
sudo dmesg | grep -iE 'rtl8723|rtlwifi|firmware|pcie'
```

Log থেকে পাওয়া Error বিশ্লেষণ করে পরবর্তী Troubleshooting করতে হবে।
