# Looking Glass Ansible Automation

Automated deployment of [Hybula Looking Glass](https://github.com/hybula/lookingglass) on Alma Linux 9 servers using Ansible.

## Features

- 🚀 One-command deployment
- 🔧 Full network tools setup (ping, traceroute, MTR, iperf3)
- 🌐 Multi-server cross-referencing
- 🎨 Custom branding and footer
- 🔒 Firewall and security configuration
- 📊 Speed test files generation

## Requirements

- **Control Machine**: Ansible 2.9+ with Python 3.6+
- **Target Servers**: Alma Linux 9 with root SSH access
- **Network**: Servers accessible via SSH and HTTP/HTTPS

## Quick Start

1. **Clone the repository**

   ```bash
   git clone https://github.com/m4hi2/lookingglass-ansible.git
   cd lookingglass-ansible
   ```

2. **Configure your inventory**

   ```bash
   cp inventory.ini.example inventory.ini
   # Edit inventory.ini with your server details
   ```

3. **Deploy everything**

   ```bash
   ansible-playbook -i inventory.ini deploy-all.yml
   ```

## Configuration

### Inventory Setup

Edit `inventory.ini` with your server details:

```ini
[lookingglass:children]
bd_servers
us_servers

[bd_servers]
lg-bd1 ansible_host=YOUR_IP lg_domain=lg-bd1.yourdomain.com lg_ipv6=YOUR_IPV6 lg_location_id="BD1 - Dhaka, Bangladesh"

[us_servers]  
lg-us1 ansible_host=YOUR_IP lg_domain=lg-us1.yourdomain.com lg_ipv6=YOUR_IPV6 lg_location_id="US1 - New York, USA"

[lookingglass:vars]
ansible_user=root
ansible_ssh_private_key_file=~/.ssh/your_key
ansible_ssh_common_args='-o StrictHostKeyChecking=no'
ansible_python_interpreter=auto_silent
```

### Required Variables per Host

| Variable | Description | Example |
|----------|-------------|---------|
| `ansible_host` | Server IP address | `192.168.1.100` |
| `lg_domain` | Domain for the Looking Glass | `lg-ny1.example.com` |
| `lg_ipv6` | IPv6 address (optional) | `2001:db8::1` |
| `lg_location_id` | Display name for location | `"US1 - New York, USA"` |

### Customization

Override default settings in `group_vars/all.yml`:

```yaml
# Company branding
lg_company_name: "Your Company"
lg_company_url: "https://yourcompany.com"
lg_logo_image_url: "https://yourcompany.com/logo.png"

# Footer customization
lg_footer_company_url: "https://yourcompany.com"
lg_footer_source_url: "https://github.com/yourusername/your-repo"
```

## Playbook Structure

```
├── deploy-all.yml              # Master deployment playbook
├── update-servers.yml          # System updates
├── install-dependencies.yml    # Install required packages
├── install-lookingglass.yml    # Install and configure Looking Glass
├── inventory.ini              # Server inventory
├── group_vars/
│   └── all.yml                # Global variables
└── README.md
```

## Individual Playbooks

Run specific parts of the deployment:

```bash
# Update all servers
ansible-playbook -i inventory.ini update-servers.yml

# Install dependencies only
ansible-playbook -i inventory.ini install-dependencies.yml

# Install Looking Glass only
ansible-playbook -i inventory.ini install-lookingglass.yml

# Target specific servers
ansible-playbook -i inventory.ini deploy-all.yml --limit bd_servers
```

## What Gets Installed

### Network Tools

- `ping` - ICMP ping utility
- `traceroute` - Network path tracing
- `mtr` - Network diagnostic tool with capabilities
- `iperf3` - Network performance testing (server mode)

### Web Stack

- `httpd` (Apache) - Web server
- `php` + `php-fpm` - PHP runtime

### Security

- `firewalld` - Firewall with HTTP/HTTPS/SSH/iperf3 ports
- SELinux configuration for web access

### Speed Test Files

- 10MB, 100MB, 1GB test files generated automatically

## Firewall Ports

| Port | Protocol | Service |
|------|----------|---------|
| 22   | TCP      | SSH |
| 80   | TCP      | HTTP |
| 443  | TCP      | HTTPS |
| 5201 | TCP      | iperf3 |

## Troubleshooting

### Common Issues

**MTR not working:**

```bash
# Check capabilities
ansible all -i inventory.ini -m shell -a "getcap /usr/sbin/mtr"

# Verify symlink
ansible all -i inventory.ini -m shell -a "ls -la /usr/bin/mtr"
```

**iperf3 connection refused:**

```bash
# Check service status
ansible all -i inventory.ini -m shell -a "systemctl status iperf3"

# Test iperf3 manually
iperf3 -c YOUR_SERVER_IP -p 5201
```

**HTTP 500 errors:**

```bash
# Check PHP syntax
ansible all -i inventory.ini -m shell -a "php -l /var/www/html/config.php"

# Check Apache logs
ansible all -i inventory.ini -m shell -a "tail -20 /var/log/httpd/error_log"
```

### Verification Commands

```bash
# Test all network tools
ansible all -i inventory.ini -m shell -a "ping -c 1 8.8.8.8"
ansible all -i inventory.ini -m shell -a "traceroute -m 5 8.8.8.8"
ansible all -i inventory.ini -m shell -a "mtr --report --report-cycles 1 8.8.8.8"

# Check web services
ansible all -i inventory.ini -m uri -a "url=http://{{ lg_domain }} method=GET"
```

## Future Plans

### SSL/TLS Setup

Add SSL certificate deployment tasks to enable HTTPS. Currently using CloudFlare provided SSL.

### Monitoring Integration

Extend playbooks to include monitoring agents (Zabbix, Nagios, etc.).

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with your infrastructure
5. Submit a pull request

## License

This project is open-source. See the original [Hybula Looking Glass](https://github.com/hybula/lookingglass) for licensing terms.

## Support

- 🐛 Issues: [GitHub Issues](https://github.com/m4hi2/lookingglass-ansible/issues)
- 💬 Discussions: [GitHub Discussions](https://github.com/m4hi2/lookingglass-ansible/discussions)
- 🌐 Demo: Visit [HostOmega Looking Glass](https://lg-bd1.hostomega.com) for a live example

## Credits

- **Looking Glass Software**: [Hybula](https://github.com/hybula/lookingglass)
- **Infrastructure**: [HostOmega](https://hostomega.com)

---

**Ready to deploy your own Looking Glass network? [Get started with HostOmega hosting!](https://hostomega.com)**
