# Ubuntu VPS-এ GUI বন্ধ করে CLI Mode চালু করা

Ubuntu VPS-এ GUI (Graphical User Interface) বন্ধ করে CLI (Command Line Interface) মোডে কনভার্ট করার জন্য **`systemctl`** কমান্ড ব্যবহার করা হয়।

GUI বন্ধ করার জন্য নিচের ধাপগুলো অনুসরণ করুন।

## ১. সাময়িকভাবে GUI বন্ধ করতে

আপনি যদি বর্তমান সেশনের জন্য GUI বন্ধ করতে চান (সার্ভার রিবুট করলে আবার GUI চালু হবে), তবে টার্মিনালে নিচের কমান্ডটি লিখুন:

```bash
sudo systemctl isolate multi-user.target
```

## ২. স্থায়ীভাবে GUI বন্ধ করতে (প্রস্তাবিত)

সার্ভার বুট হওয়ার সময় যেন সরাসরি কমান্ড লাইন (CLI) চালু হয় এবং GUI ব্যাকগ্রাউন্ডে RAM ব্যবহার না করে, সেজন্য নিচের কমান্ডটি দিন:

```bash
sudo systemctl set-default multi-user.target
```

এই কমান্ডটি দেওয়ার পর পরিবর্তনটি কার্যকর করতে সার্ভারটি একবার রিবুট করতে হবে:

```bash
sudo reboot
```

---

## 💡 অতিরিক্ত টিপস

### ভবিষ্যতে আবার GUI চালু করতে চাইলে

যদি কখনো আবার GUI ফেরত আনতে চান, তবে নিচের কমান্ডটি দিয়ে বুটের ডিফল্ট টার্গেট পরিবর্তন করুন:

```bash
sudo systemctl set-default graphical.target
```

### চলতি সেশনেই GUI আবার চালু করতে

রিবুট না করে সরাসরি GUI চালু করতে নিচের কমান্ডটি লিখুন:

```bash
sudo systemctl isolate graphical.target
```

---

## ❓ আপনার VPS সম্পর্কে কিছু তথ্য

আপনার VPS-এ বর্তমানে কোন Desktop Environment ইনস্টল করা আছে?

উদাহরণ:
- GNOME
- XFCE / Xubuntu
- MATE

আপনি কি RAM বাঁচানোর জন্য Desktop Environment সম্পূর্ণ আনইনস্টল করে দিতে চান, নাকি শুধু বুট অপশন বন্ধ রাখাই যথেষ্ট?

জানালে সেই অনুযায়ী সাহায্য করা যাবে।
