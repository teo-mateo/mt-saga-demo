# Business Case: Online Marketplace Order Fulfillment System

## Business Context
**TechMart** is an online electronics marketplace that connects customers with various suppliers. When a customer places an order, multiple backend systems must coordinate to ensure successful fulfillment. The process involves inventory validation, payment processing, warehouse allocation, and shipping coordination.

## The Business Process

### Order Placement Flow

1. **Customer Order Submission**
   - Customer adds items to cart (laptops, phones, accessories)
   - Proceeds to checkout with delivery preferences
   - System must validate and process the order

2. **Inventory Validation** (Request-Response with InventoryService)
   - Check real-time stock levels across multiple warehouses
   - Reserve inventory for 30 minutes
   - Handle partial availability scenarios
   - Manage competing orders for same inventory

3. **Payment Processing**
   - Authorize credit card/payment method
   - Handle payment failures and retries
   - Support partial refunds for unavailable items

4. **Warehouse Allocation**
   - Select optimal warehouse based on location
   - Generate picking instructions
   - Coordinate with fulfillment centers

5. **Shipping Arrangement**
   - Book courier service
   - Generate tracking information
   - Send confirmation to customer

## Why a Saga?

This process requires:
- **Long-running transactions** (minutes to hours)
- **Compensating actions** (unreserve inventory, refund payments)
- **State persistence** across service restarts
- **Coordination** between autonomous services
- **Resilience** to partial failures

## Key Business Rules

1. **Inventory Reservation**
   - Must be atomic across multiple items
   - Expires after 30 minutes if not confirmed
   - Supports partial fulfillment with customer consent

2. **Payment Authorization**
   - Only capture after successful inventory reservation
   - Support split payments for partial orders
   - Automatic refund on fulfillment failure

3. **Fulfillment SLA**
   - Orders must be processed within 2 hours
   - Customer notification required for any delays
   - Escalation path for stuck orders

## Failure Scenarios & Business Impact

1. **Inventory Service Unavailable**
   - Business Impact: Orders stuck, customer frustration
   - Required: Timeout and retry logic, customer notification

2. **Payment Gateway Timeout**
   - Business Impact: Money held but order not confirmed
   - Required: Idempotent payment handling, reconciliation

3. **Warehouse System Crash**
   - Business Impact: Reserved inventory but no fulfillment
   - Required: State recovery, automatic reassignment

4. **Concurrent Order Processing**
   - Business Impact: Overselling of limited inventory
   - Required: Distributed locking, eventual consistency handling

5. **Network Partition During Saga**
   - Business Impact: Inconsistent order state
   - Required: Saga state recovery, compensating transactions

## Success Metrics

- **Order Processing Time**: < 2 hours from submission to fulfillment
- **Inventory Accuracy**: 99.9% real-time availability
- **Payment Success Rate**: > 95% first-attempt authorization
- **Customer Satisfaction**: < 0.1% order-related complaints

## Technical Requirements

This business case provides rich scenarios for demonstrating:
- Saga orchestration patterns
- Request-response communication patterns
- Compensating transactions
- Distributed transaction handling
- State persistence and recovery
- Timeout and retry strategies
- Eventual consistency management