![Header](./images/header.png)
(Generated with [PixelPoSH](https://github.com/dabeastnet/PixelPoSH))

# SolarPVE

A **[Solarized](https://ethanschoonover.com/solarized/)**-inspired light & dark CSS theme for Proxmox VE’s web UI.  
Automatically switches between light + dark themes based on system preference.

---

## Screenshots

Lightmode:
![Lightmode](./images/light.png)

Darkmode:
![Darkmode](./images/dark.png)

## Features

- **Light** (Solarized Light) when selected
- **Dark** (Solarized Dark) when selected
- **Auto** - when selected, automatically chooses theme based on user/system preferences, via the `prefers-color-scheme: dark` attribute
- No JS-framework dependencies—just vanilla CSS & a tiny inline script
- Easy to install and override via Proxmox’s `index.html.tpl`

---

## Installation

### Manual installation

1. **Copy the CSS**  
   Place `solarized.css` into your Proxmox server’s static‐assets folder.  
   By default this is:

```

/usr/share/pve-manager/images/solarized.css

```

2. **Patch the Proxmox template**  
   Edit (or better: override) Proxmox’s `index.html.tpl`—usually at:

```

/usr/share/pve-manager/index.html.tpl

```

and add _before_ the existing `</body>` (near the bottom of the document) the following snippet:

```html
<script>
  document.addEventListener("DOMContentLoaded", () => {
    // Detect if Proxmox’s dark stylesheet is loaded
    const darkLink = document.querySelector(
      'link[href*="theme-proxmox-dark.css"]',
    );
    if (darkLink) {
      document.body.classList.add("proxmox-theme-dark");
    }
  });
</script>

<link rel="stylesheet" href="/pve2/images/solarized.css" />
```

(An example index.html.tpl ships in this repo, see the `Example` folder)

3. **Clear browser cache**
   After restarting the PVE manager service, do a hard-refresh (Ctrl+F5) so your new CSS and script take effect.

4. **Restart Proxmox GUI**

   ```bash
   systemctl restart pveproxy
   ```

### Ansible playbook (auto installer)

> See the ansible subfolder

#### 🛠️ Prerequisites

- **Ansible 2.9+** installed on your control machine
- SSH access (with `become`/sudo rights) to one or more Proxmox hosts
- A copy of your `solarized.css` in `files/solarized.css`

#### ⚙️ Usage

1. **Add** your custom CSS:

   ```bash
   # Copy your tailored Solarized stylesheet into the files/ directory
   cp ./solarized.css ./ansible/playbooks/files
   ```

2. **Run** the playbook against your Proxmox inventory:

   ```bash
   ansible-playbook -i hosts solarized-theme.yml --limit proxmox
   ```

   > This will:
   >
   > - Copy `solarized.css` to `/usr/share/pve-manager/images/solarized.css`
   > - Patch Proxmox’s `index.html.tpl` to include light & dark-mode logic
   > - Restart `pveproxy` to serve your new theme

---

#### 🔧 Configuration

- **Playbook file**: `solarized-theme.yml`
- **Key variables** (override via `-e` or `group_vars` if desired):

  | Variable   | Default path                                      |
  | ---------- | ------------------------------------------------- |
  | `css_src`  | `files/solarized.css`                             |
  | `css_dest` | `/usr/share/pve-manager/images/solarized.css`     |
  | `tpl_path` | `/usr/share/pve-manager/templates/index.html.tpl` |

---

## How It Works

- Determines selected theme via the `PVEThemeCookie` value that is set when choosing light, dark or default.

- **Light mode**
  All rules scoped under `body:not(.proxmox-theme-dark)` apply Solarized Light defaults.

- **Dark mode**
  When detected, the `<script>` adds `.proxmox-theme-dark` to `<body>` and you can write complementary CSS rules (e.g. under `body.proxmox-theme-dark`) for Solarized Dark.

- **Auto Mode**
  Dynamically changes theme between light + dark modes based on system preferences. Checks for the presence of the `prefers-color-scheme: dark` attribute.

---

## Customization

- Feel free to edit any of the `--baseXX` or `--accent` variables at the top of `solarized.css` to tweak colors.
- To add new dark-mode overrides, target:

  ```css
  body.proxmox-theme-dark .your-selector {
    /* dark-mode styles here */
  }
  ```

---

## Contributing

1. Fork this repo
2. Create a feature branch (`git checkout -b my-tweak`)
3. Commit your changes (`git commit -am 'Add foo'`)
4. Push and open a Pull Request

Bug reports and PRs for improved selectors, additional components, or accessibility tweaks are very welcome!

---

## License

This repo is licensed under **CC BY-NC 4.0**.

- ✅ Free to use, modify & share for **non-commercial** purposes (with attribution).
- ❗️ For commercial use please see the license file

---

Enjoy a Solarized-themed Proxmox UI! 🚀
