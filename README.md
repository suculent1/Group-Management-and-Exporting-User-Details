Project 2: Active Directory Group Management and Exporting User Details with PowerShell
Description
This project demonstrates how to use PowerShell to manage groups in Active Directory. It includes functionalities for creating new groups, adding users to groups, and exporting user details to a CSV file.

Prerequisites
Ensure you have the Active Directory module installed. You can install it using the following command if it's not already available:

powershell
Copy code
Install-WindowsFeature -Name RSAT-AD-PowerShell
Script: Active Directory Group Management and Exporting User Details
powershell
Copy code
# Import the Active Directory module
Import-Module ActiveDirectory

# Set AD domain details
$domain = "yourdomain.local"

# Function to create a new group
function New-ADGroup {
    param (
        [string]$GroupName,
        [string]$Description,
        [string]$GroupScope = "Global",
        [string]$GroupCategory = "Security"
    )

    # Check if group already exists
    if (Get-ADGroup -Filter {Name -eq $GroupName} -ErrorAction SilentlyContinue) {
        Write-Host "Group $GroupName already exists."
        return
    }

    # Create the new group
    New-ADGroup -Name $GroupName `
                -GroupScope $GroupScope `
                -GroupCategory $GroupCategory `
                -Description $Description `
                -Path "CN=Users,DC=yourdomain,DC=local"

    Write-Host "Group $GroupName created successfully."
}

# Function to add a user to a group
function Add-UserToGroup {
    param (
        [string]$Username,
        [string]$GroupName
    )

    # Check if user exists
    $user = Get-ADUser -Filter {SamAccountName -eq $Username}
    if ($null -eq $user) {
        Write-Host "User $Username does not exist."
        return
    }

    # Check if group exists
    $group = Get-ADGroup -Filter {Name -eq $GroupName}
    if ($null -eq $group) {
        Write-Host "Group $GroupName does not exist."
        return
    }

    # Add user to group
    Add-ADGroupMember -Identity $GroupName -Members $Username
    Write-Host "User $Username added to group $GroupName successfully."
}

# Function to export user details to CSV
function Export-UserDetailsToCSV {
    param (
        [string]$CSVPath
    )

    # Get all users in the domain
    $users = Get-ADUser -Filter * -Property SamAccountName, GivenName, Surname, EmailAddress, Department, Title

    # Select user details
    $userDetails = $users | Select-Object SamAccountName, GivenName, Surname, EmailAddress, Department, Title

    # Export to CSV
    $userDetails | Export-Csv -Path $CSVPath -NoTypeInformation
    Write-Host "User details exported to $CSVPath successfully."
}

# Example usage
# Creating a new group
New-ADGroup -GroupName "IT_Staff" `
            -Description "Group for IT Department Staff"

# Adding a user to a group
Add-UserToGroup -Username "jdoe" -GroupName "IT_Staff"

# Exporting user details to CSV
Export-UserDetailsToCSV -CSVPath "C:\ADUserDetails.csv"
Instructions for Running the Script
Save the script to a .ps1 file, for example, ADGroupManagement.ps1.
Open PowerShell with administrative privileges.
Navigate to the directory containing the script.
Run the script using the following command:
powershell
Copy code
.\ADGroupManagement.ps1
Notes
Replace yourdomain.local and paths with actual values from your Active Directory environment.
Ensure you have the necessary permissions to perform AD management tasks.
This project provides a comprehensive example of how to automate group management in Active Directory and export user data to a CSV file using PowerShell.