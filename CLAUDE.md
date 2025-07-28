# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repository contains a Pokemon inventory management tool called RoninVault. It consists of:
- An HTML-based Pokemon inventory processor with Google Sheets integration
- CSV data files for Pokemon card inventory tracking

## Key Files

- **pokemon-inventory-processor (2).html**: Main application file containing the Pokemon inventory management interface with Google Sheets sync capabilities
- **REF_test - Sheet1.csv**: Reference data for Pokemon cards including serial numbers, grading information, prices, and media links
- **pokemon_pack_25Jul.csv**: Transaction data for Pokemon pack purchases including payment details, buyback information, and claim status

## Architecture

The application is a single-page HTML application that:
1. Processes Pokemon card inventory data from CSV files
2. Tracks duplicates and manages card collections
3. Integrates with Google Sheets for automatic synchronization
4. Handles both reference data (card catalog) and transaction data (pack purchases)

## Data Structure

### Reference Data (REF_test - Sheet1.csv)
- Contains Pokemon card information including serial numbers, grading details, prices
- Includes media links (videos, images) and external URLs

### Transaction Data (pokemon_pack_25Jul.csv)  
- Tracks pack purchases with payment tokens (USDC, RON)
- Records buyback transactions and claim status
- Includes recipient addresses and transaction hashes
- Status column indicates: "claimed" (needs shipping to vault), "buyback" (sold back, no action needed), or blank (owned but not claimed)

## Development Notes

This is a client-side web application with no build process required. The HTML file can be opened directly in a browser to use the inventory processing functionality.